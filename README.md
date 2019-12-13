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

<h2> Microservice 1 </h2>

<b>touch queries-pg.js</b>

/**
 * https://blog.logrocket.com/setting-up-a-restful-api-with-node-js-and-postgresql-d96d6fc892d8/
 * 
 * 
 * https://imasters.com.br/apis-microsservicos/api-gateway-em-arquitetura-de-microservices-com-node-js
 * 
 * https://medium.com/caquicoders/criando-um-simples-api-gateway-com-nodejs-e-express-2ec5369e975d
 * 
 */
const Pool = require('pg').Pool
const httpStatusCode = require('./http-status-code.js')

const pool = new Pool({
    user: '',
    host: '',
    database: '',
    password: '',
    port: 
})

const getUsers = (request, response) => {
    console.log('Finding all users - start')
    pool.query('select * from TABELA  order by NAME_COLUM', (error, results) => {
        if (error) {
            throw error
        }
        response.status(200).json(results.rows)
        console.log('Finding all users - finish')
    })
}

const getReunions = (request, response) => {
    console.log('Finding all reunions - start')
    pool.query('select * from TABELA2  order by NAME_COLUM2', (error, results) => {
        if (error) {
            throw error
        }
        response.status(200).json(results.rows)
        console.log('Finding all reunions - finish')
    })
}

module.exports = {
    getUsers,
    getReunions
}


<b>touch index.js</b>

```
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
```

<h2> Microservice 2 </h2>


<h1> Gateway </h1>

<h3> Constants URLS - Microservices </h3>

touch URL.js

```
module.exports = {
  URL_API_MICROSERVICE_1: 'http://localhost:3001',
  URL_API_MICROSERVICE_2: 'http://localhost:3002',
};

```

```
npm install http express morgan helmet express-http-proxy cookie-parser
```


<b>index.js</b>

touch index.js

```

var http = require('http');
const express = require('express')
const httpProxy = require('express-http-proxy')
const app = express()
var cookieParser = require('cookie-parser');
var logger = require('morgan');
const helmet = require('helmet');
const {
  URL_API_MICROSERVICE_1,
  URL_API_MICROSERVICE_2,
} = require('./URLs');

const userviceProxy1 = httpProxy(URL_API_MICROSERVICE_1);
const userviceProxy2 = httpProxy(URL_API_MICROSERVICE_2);

// Proxy request
app.get('/users', (req, res, next) => {
  userviceProxy1(req, res, next);
})

app.get('/reunions', (req, res, next) => {
  userviceProxy1(req, res, next);
})

app.get('/TODO', (req, res, next) => {
  userviceProxy2(req, res, next);
})

app.use(logger('dev'));
app.use(helmet());
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());

var server = http.createServer(app);
server.listen(3000);

```
