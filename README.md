This project is a fork of [Swagger-Stats](https://github.com/slanatech/swagger-stats)

## About the fork
The Original project [Swagger-Stats](https://github.com/slanatech/swagger-stats) has not been maintained since Jul 2,2021.
It resulted in multiple outdated NPM references. The purpose of this brunch is to keep the project up to date. I hope 
original project will be maintained soon.


<p align="center">
<img src="https://github.com/slanatech/swagger-stats/blob/master/screenshots/logo.png?raw=true" alt="swagger-stats"/>
</p>

# swagger-stats | API Observability


####  [https://swaggerstats.io](https://swaggerstats.io) | [Guide](https://swaggerstats.io/guide/) 

[![Build Status](https://travis-ci.org/slanatech/swagger-stats.svg?branch=master)](https://travis-ci.org/slanatech/swagger-stats)
[![Coverage Status](https://coveralls.io/repos/github/slanatech/swagger-stats/badge.svg?branch=master&dummy)](https://coveralls.io/github/slanatech/swagger-stats?branch=master&dummy)
[![npm version](https://badge.fury.io/js/swagger-stats.svg)](https://badge.fury.io/js/swagger-stats)
[![npm downloads](https://img.shields.io/npm/dm/swagger-stats.svg)](https://img.shields.io/npm/dm/swagger-stats)
[![Gitter](https://badges.gitter.im/swagger-stats/community.svg)](https://gitter.im/swagger-stats/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)


#### Trace API calls and Monitor API performance, health and usage statistics in Node.js Microservices

### Express, Fastify, Koa, Hapi, Restify

**swagger-stats** traces REST API requests and responses in Node.js Microservices, and collects statistics per API Operation.
**swagger-stats** detects API operations based on express routes. You may also provide [Swagger (Open API) specification](https://swagger.io/specification/), 
and swagger-stats will match API requests with API Operations defined in swagger specification. 

**swagger-stats** exposes statistics and metrics per API Operation, such as `GET /myapi/:parameter`, or `GET /pet/{petId}`
 

### Built-In API Telemetry 

> **swagger-stats** provides built-in Telemetry UX, so you may enable **swagger-stats** in your app, and start monitoring immediately, with no infrastructure requirements.
> Navigate to `http://<your app host:port>/swagger-stats/`   


![swagger-stats Built-In Telemetry](screenshots/swsux.gif?raw=true)


       
### API Analytics with [Elasticsearch](https://www.elastic.co/) and [Kibana](https://www.elastic.co/products/kibana)

> **swagger-stats** stores details about each request/response in [Elasticsearch](https://www.elastic.co/), so you may use [Kibana](https://www.elastic.co/products/kibana) 
> to perform detailed analysis of API usage over time, build visualizations and dashboards


![swagger-stats Kibana Dashboard](screenshots/kibana.gif?raw=true)

See `dashboards/elastic6` for swagger-stats Kibana visualizations and dashboards
 

### Monitoring and Alerting with [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/)

> **swagger-stats** exposes metrics in [Prometheus](https://prometheus.io/) format, so you may use [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/) to setup API monitoring and alerting


![swagger-stats Prometheus Dashboard](screenshots/prometheus-dashboard-2-sm.png?raw=true)


See `dashboards/prometheus` for swagger-stats Grafana dashboards 



With statistics and metrics exposed by **swagger-stats** you may spot problematic API endpoints, see where most of errors happens, 
catch long-running requests, analyze details of last errors, observe trends, setup alerting. 

 
**swagger-stats** provides:
* Metrics in [Prometheus](https://prometheus.io/) format, so you may use [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/) to setup API monitoring and alerting
* Storing details about each API Request/Response in [Elasticsearch](https://www.elastic.co/), so you may use [Kibana](https://www.elastic.co/products/kibana) to perform analysis of API usage over time, build visualizations and dashboards  
* Built-in API Telemetry UI, so you may enable swagger-stats in your app, and start monitoring right away, with no additional tools required
* Exposing collected statistics via API, including:
* Counts of requests and responses(total and by response class), processing time (total/avg/max), 
content length(total/avg/max) for requests and responses, rates for requests and errors. 
This is baseline set of stats. 
* Statistics by Request Method: baseline stats collected for each request method
* Timeline: baseline stats collected for each 1 minute interval during last 60 minutes. Timeline helps you to analyze trends.
* Errors: count of responses per each error code, top "not found" resources, top "server error" resources
* Last errors: request and response details for the last 100 errors (last 100 error responses)
* Longest requests: request and response details for top 100 requests that took longest time to process (time to send response)
* Tracing: Request and Response details - method, URLs, parameters, request and response headers, addresses, start/stop times and processing duration, matched API Operation info
* API Statistics: baseline stats and parameter stats per each API Operation. API operation detected based on express routes, and based on [Swagger (Open API) specification](https://swagger.io/specification/) 
* CPU and Memory Usage of Node process


## How to Use 


### Install 

```
npm install swagger-stats --save
```

If you haven't added prom-client already, you should do this now. It's a [peer dependency](https://nodejs.org/en/blog/npm/peer-dependencies) of swagger-stats as of version [0.95.19](https://github.com/slanatech/swagger-stats/releases/tag/v0.95.19).

```
npm install prom-client@12 --save
```

### Enable swagger-stats middleware in your app

#### Express

```javascript
const swStats = require('swagger-stats');
const apiSpec = require('swagger.json');
app.use(swStats.getMiddleware({swaggerSpec:apiSpec}));
```

#### Fastify

```javascript
const swStats = require('swagger-stats');
const apiSpec = require('swagger.json');

const fastify = require('fastify')({
    logger: true
});

// Enable swagger-stats
fastify.register(require('fastify-express')).then(()=>{
    fastify.register(swStats.getFastifyPlugin, {swaggerSpec:apiSpec});
});

```


#### Koa

[`express-to-koa`](https://github.com/kaelzhang/express-to-koa) can be used which is just a simple `Promise` wrapper.

```javascript
const swStats = require('swagger-stats');
const apiSpec = require('swagger.json');
const e2k = require('express-to-koa');
app.use(e2k(swStats.getMiddleware({ swaggerSpec:apiSpec })));
```

#### Hapi

```javascript
const swStats = require('swagger-stats');
const swaggerSpec = require('./petstore.json');

const init = async () => {

    server = Hapi.server({
        port: 3040,
        host: 'localhost'
    });

    await server.register({
        plugin: swStats.getHapiPlugin,
        options: {
             swaggerSpec:swaggerSpec
        }
    });

    await server.start();
    console.log('Server running on %s', server.info.uri);
};
````

#### Restify

```javascript
const restify = require('restify');
const swStats = require('swagger-stats');
const apiSpec = require('swagger.json');

const server = restify.createServer();

server.pre(swStats.getMiddleware({
    swaggerSpec:apiSpec,
}));
```

See `/examples` for sample apps

### Get Statistics with API


```
$ curl http://<your app host:port>/swagger-stats/stats
{
  "startts": 1501647865959,
  "all": {
    "requests": 7,
    "responses": 7,
    "errors": 3,
    "info": 0,
    "success": 3,
    "redirect": 1,
    "client_error": 2,
    "server_error": 1,
    "total_time": 510,
    "max_time": 502,
    "avg_time": 72.85714285714286,
    "total_req_clength": 0,
    "max_req_clength": 0,
    "avg_req_clength": 0,
    "total_res_clength": 692,
    "max_res_clength": 510,
    "avg_res_clength": 98,
    "req_rate": 1.0734549915657108,
    "err_rate": 0.4600521392424475
  },
  "sys": {
    "rss": 59768832,
    "heapTotal": 36700160,
    "heapUsed": 20081776,
    "external": 5291923,
    "cpu": 0
  },
  "name": "swagger-stats-testapp",
  "version": "0.90.1",
  "hostname": "hostname",
  "ip": "127.0.0.1"
}
```

Take a look at [Documentation](https://swaggerstats.io/guide/) for more details on API and returned statistics.


### Get Prometheus Metrics 


```
$ curl http://<your app host:port>/swagger-stats/metrics
# HELP api_all_request_total The total number of all API requests received
# TYPE api_all_request_total counter
api_all_request_total 88715
# HELP api_all_success_total The total number of all API requests with success response
# TYPE api_all_success_total counter
api_all_success_total 49051
# HELP api_all_errors_total The total number of all API requests with error response
# TYPE api_all_errors_total counter
api_all_errors_total 32152
# HELP api_all_client_error_total The total number of all API requests with client error response
# TYPE api_all_client_error_total counter
api_all_client_error_total 22986

. . . . . . . . . .  

```

#### Default Metrics


To collect [prom-client default metrics](https://github.com/siimon/prom-client#default-metrics):

```javascript
const swaggerStats = require('swagger-stats');
const promClient = require('prom-client');

promClient.collectDefaultMetrics();
```

Some Node.js specific metrics are included, such as event loop lag:

```
# HELP nodejs_eventloop_lag_seconds Lag of event loop in seconds.
# TYPE nodejs_eventloop_lag_seconds gauge
nodejs_eventloop_lag_seconds 0.000193641 1597303877464

. . . . . . . . . .  

```


## Updates 

See [Changelog](https://github.com/slanatech/swagger-stats/blob/master/CHANGELOG.md)

## Enhancements and Bug Reports

If you find a bug, or have an enhancement in mind please post [issues](https://github.com/slanatech/swagger-stats/issues) on GitHub.

## License
 
MIT
