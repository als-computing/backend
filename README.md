# SciCat Metadata Server
[![Build Status](https://travis-ci.org/SciCatProject/catamel.svg?branch=develop)](https://travis-ci.org/SciCatProject/catamel)
[![Known Vulnerabilities](https://snyk.io/test/github/SciCatProject/catamel/develop/badge.svg?targetFile=package.json)](https://snyk.io/test/github/SciCatProject/catamel/develop?targetFile=package.json)

The data catalog backend is based on [NoSQL database MongoDB](https://www.mongodb.com/), [the Node based web application framework Express](http://expressjs.com/) and [the API framework Loopback](http://loopback.io/) technology stack (the "MEL" part of the "MELANIE" technology stack). It provides a REST based API which allows to store information about datasets and to allow to answer queries about the stored meta data for these datasets

# Getting started

## Prerequisites

First you need to have node/npm installed

```
npm version 6 or higher
Node version 10 or higher
```
You also need to setup a MongoDB server. E.g. on a Redhat Linux System the following command will suffice

```
yum install mongodb-server
```

On MacOS 
```
brew install mongodb
```


The needed database will be created automatically when the API server starts. Follow this [description](https://docs.mongodb.com/manual/tutorial/enable-authentication/) to enable authenticated access to the Mongo DB.

## Get code
```
git clone https://github.com/SciCatProject/catamel.git
cd catamel
git checkout master # (or develop)
npm install
```

## Setup configuration
You can start with the sample files:

```
cd server
cp datasources.json-sample datasources.json
cp providers.json-sample providers.json
cp config.local.js-sample config.local.js
```

Then edit these files and adjust the variables to meaningful values for your site/domain


## Start metadata server
```
node .
```

## Queuing Options

Jobs in Catamel are published to a queue when they are received. There is no default in place.

Without setting this up, none of your job submissions will not go anywhere.

The two supported options are:
1. RabbitMQ (loopback-component-mq)
2. Apache Kafka (loopback-connector-kafka)

Both packages are installed at time of install and you can select a queue in `config.local.js`, like this:

`queue: "rabbitmq" // also accepts "kafka" or null`


### Rabbit

1. Set `config.local.js` queue value to "rabbitmq".
2. Provide configuration for rabbitmq as a new block in `component-config.json`. NOTE: Make sure this is empty

### Kafka

1. Set `config.local.js` queue value to "kafka".
2. Instructions for configuring Kafka can be found [here](https://www.npmjs.com/package/loopback-connector-kafka)

* Creating a datasource programatically in the model you want to attach will often be the easiest solution.

NOTE: An example of kafka has been set in `Job.js`

# Data models

The data model is defined inside the common/models directory according to the rules defined by the Loopback API framework
The data model is visualized in form of an

Model UML diagram which can be seen at <catamel_url:port>/modeldiagram or
![model diagram](https://github.com/SciCatProject/catamel/blob/develop/CI/ESS/scicatmodeldiagram.png)

Model Visualizer which can be seen at <catamel_url:port>/visualize 

# Data Catalog API

## REST API

The REST API can be tested via the [Explorer webpage](http://localhost:3000/explorer)
The [OpenAPI definition](https://www.openapis.org/) can be fetched from  [the Swagger Definition link](http://localhost:3000/explorer/swagger.json)

## Python Client API
The API is automatically created from the Swagger/openAPI (http://swagger.io/) specificication created from the data model using the [Swagger Codegen code](https://github.com/swagger-api/swagger-codegen)

```
java -jar ./swagger-codegen/modules/swagger-codegen-cli/target/swagger-codegen-cli.jar generate -i swagger.json -l python -o dacat-api/client/python
```

See e.g. the [description of Dataset model](https://gitlab.psi.ch/MELANIE/catamel/blob/master/client/python/docs/Dataset.md)

## Angular 2 Client API
The library is generated by the following command
```
./node_modules/.bin/lb-sdk server/server.js  client/angular2/sdk
```

## Authentication
Users are authenticated by loopback built in user accounts and by accounts, which are connected via any of the passport supported strategies,e.g. OpenID connected and a direct AD connection. The providers for this must be defined in the providers.json file above.

In addition the so called functional accounts,like the ingestor are automatically created at boot time with a default password "aman". You can get an API access token via the Users login API endpoint.

