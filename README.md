# node-api-geatway
A node api gateway to manager micoservices


<h2> Gateway Architecture Model </h2>

<img src="https://static.imasters.com.br/wp-content/uploads/2018/06/25111108/api-gateway-1.png" alt="Smiley face">


<h2> Gateway Architecture Model - Used By Netflix</h2>

<img src="https://static.imasters.com.br/wp-content/uploads/2018/06/25111152/paved-paas-to-microservices-7-638.jpg" alt="Smiley face">

<br/>

<h2> Evolutionary Model - Gateway Architecture Model</h2>
<img src="https://static.imasters.com.br/wp-content/uploads/2018/06/25111307/api-gateway-evolutinary-design.png" alt="Smiley face">



<h2> Plugins </h2>
http [create a server http basic]</br>
express [create routes api]</br>
morgan [log]</br>
helmet [protect atacks]</br>
express-http-proxy [invoke others end points]</br>
cookie-parser [cookie parsing requests]</br>

```
npm install http express morgan helmet express-http-proxy cookie-parser
```


<h2> Constants URLS - Microservices </h2>

<h2> Microservice 1 </h2>
const express = require('express')
const bodyParser = require('body-parser')
const app = express()
const port = 3001
const dbPg = require('./queries-pg.js')


app.use(bodyParser.json())
app.use(
    bodyParser.urlencoded({
        extended : true,
    })
)

app.get('/', (request, response) => {
    response.json({ info: 'Node.js, Express, Oracle and Postgres API' })
})

app.get('/users', dbPg.getUsers)
#app.get('/reunions', dbPg.getReunions)

app.listen(port, () => {
    console.log(`App running on port ${port}.`)
  })

<h2> Microservice 2 </h2>


<h2> Gateway </h2>
touch URL.js
module.exports = {
  URL_API_1: 'http://localhost:3001',
  URL_API_1: 'http://localhost:3002',
};


<b>index.js</b>
var http = require('http');
const express = require('express')
const httpProxy = require('express-http-proxy')
const app = express()
var cookieParser = require('cookie-parser');
var logger = require('morgan');
const helmet = require('helmet');

const userServiceProxy = httpProxy('http://localhost:3001');
const productsServiceProxy = httpProxy('http://localhost:3002');

// Proxy request
app.get('/users', (req, res, next) => {
  userServiceProxy(req, res, next);
})

app.get('/products', (req, res, next) => {
  productsServiceProxy(req, res, next);
})

app.use(logger('dev'));
app.use(helmet());
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());

var server = http.createServer(app);
server.listen(3000);
