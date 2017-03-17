```js
var express = require('express')
var app = express()
var jsonServer = require('json-server')
var server = jsonServer.create()
var router = jsonServer.router('./json-server/db.json')
var middlewares = jsonServer.defaults({static: './dist'})

//Set default middlewares (logger, static, cors and no-cache)
server.use(middlewares)

//url query参数映射字典
var paramsMapper = {
	"page": "_page",
	"offset": "_start",
	"size": "_limit",
	"query": "q"
};

server.get('/api/*', function(req, res, next) {
	var p = req.path;
	//删除 '/api'
	p = p.substring(4);
	req.url = p;

	var oldQuery = req.query;
	var newQuery = {};
	for (var i in oldQuery) {
		var paramValue = oldQuery[i];
		var paramName = i;
		console.log(paramName);
		if (i in paramsMapper) {
			//需要映射的参数
			var paramName = paramsMapper[i];
		}
		newQuery[paramName] = paramValue;
	}
	req.query = newQuery;

	next();
})

server.use(jsonServer.rewriter({
	'/api/': '/'
}))

server.use(router)
server.listen(3001, function() {
	console.log('JSON Server is running...')
})
```
