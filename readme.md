# Mokker
Mokker is a simple express Reast API mock server, which also provides few methods to make your data emulating easier.

## Instalation
```
npm i --D mokker
yarn add -D mokker
```

## Dependencies
- nodemon !!!globally
- body-parser
- express
- morgan
- query-string

## Usage

```
// index.js
const mokker = require('mokker');

const routes = [{
    method: 'get',
    url: '/api',
    json: { is: 'done' }
}];

mokker.start({ routes });
// done 😍
```

`$ nodemon index.js`

## API

### .start(object)
Start the server

#### port
Type: `number`\
Default: `3000`\
Set the port number where the APi will accessible

#### routes
Type: `array`\
Default: `[]`\
Set endpoints and how they should response to a client request

##### method: string
A route method is derived from one of the HTTP methods.

##### url: string
A route path, define the endpoints at which requests can be made. Supports query parametres.

##### controller: function
A request method, how the endpoints should responde to the request. The same controller as in Express Routing.
```
const controller = (req, res) => {
    // params – params in query string /api/:id
    // query - vars in query string /api?x=1
    // body – body of the request

    const { params, query, body } = req;
    body.id = params.id;
    res.json(body); // send body as JSON
};
```

##### json: object
static JSON, which will be returned.

### .controllerQueryCondition(object)
Create the controller with query string conditions

```
const server = require('./mock');

const controller = (req, res) => {
    server.controllerQueryCondition({
        req,
        key: '@x',
        reject: () => res.json({ 'message': 'reject' }), // invoked if rejects the condition
        resolvers: [
            {
                value: 1,
                resolve: () => res.json({ 'value': 1 }) // invoked if @x = 1
            },
            {
                value: 2,
                resolve: () => res.json({ 'value': 2 }) // invoked if @x = 2
            }
        ]
    });
};

```

#### req: object
Type: `object`\
Required\
Taken from the arguments

#### key: string
The query string key

#### reject: function
The controller if rejects the condition

#### resolvers: array
The array of objects with value and resolve function

##### value: string
The query string value

##### resolve: function
The controller is fired if key and value are equal to query string

## Examples
### Simple GET request
```
// index.js
const server = require('mokker');

const routes = [{
    method: 'get',
    url: '/api',
    json: { is: 'done' }
}];

server.start({ routes });
```

### GET request with query string conditions
```
// index.js
// if host url has ?@x=1 it will get { 'value': 1 }
// if host url has ?@x=2 it will get { 'value': 2 }
// if host url has ?@x=3 it will get { 'message': 'reject' }

const server = require('mokker');

const controller = (req, res) => {
    server.controllerQueryCondition({
        req,
        key: '@x',
        reject: () => res.json({ 'message': 'reject' }),
        resolvers: [
            {
                value: 1,
                resolve: () => res.json({ 'value': 1 })
            },
            {
                value: 2,
                resolve: () => res.json({ 'value': 2 })
            }
        ]
    });
};

const routes = [{
    method: 'get',
    url: '/api',
    controller
}];

server.start({ routes });
```
### Simple POST request
```
// index.js

const server = require('mokker');

const controller = (req, res) => {
    const { body } = req;
    body.id = +new Date;
    res.json(body);
};

const routes = [{
    method: 'get',
    url: '/api',
    controller
}];

server.start({ routes });
```


## License
MIT