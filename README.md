# api documentation for  [co-body (v5.1.1)](https://github.com/cojs/co-body#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-co-body.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-co-body) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-co-body.svg)](https://travis-ci.org/npmdoc/node-npmdoc-co-body)
#### request body parsing for co

[![NPM](https://nodei.co/npm/co-body.png?downloads=true)](https://www.npmjs.com/package/co-body)

[![apidoc](https://npmdoc.github.io/node-npmdoc-co-body/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-co-body_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-co-body/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-co-body/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-co-body/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "bugs": {
        "url": "https://github.com/cojs/co-body/issues"
    },
    "dependencies": {
        "inflation": "^2.0.0",
        "qs": "^6.4.0",
        "raw-body": "^2.2.0",
        "type-is": "^1.6.14"
    },
    "description": "request body parsing for co",
    "devDependencies": {
        "istanbul": "^0.4.5",
        "koa": "^1.2.5",
        "mocha": "^3.2.0",
        "safe-qs": "^6.0.1",
        "should": "^11.2.0",
        "supertest": "^1.0.1"
    },
    "directories": {},
    "dist": {
        "shasum": "d97781d1e3344ba4a820fd1806bddf8341505236",
        "tarball": "https://registry.npmjs.org/co-body/-/co-body-5.1.1.tgz"
    },
    "files": [
        "index.js",
        "lib/"
    ],
    "gitHead": "3f40376bdcf7ea8d5baf4b636d6ed9ddadaf46cb",
    "homepage": "https://github.com/cojs/co-body#readme",
    "keywords": [
        "request",
        "parse",
        "parser",
        "json",
        "co",
        "generators",
        "urlencoded"
    ],
    "license": "MIT",
    "maintainers": [
        {
            "name": "tjholowaychuk",
            "email": "tj@vision-media.ca"
        },
        {
            "name": "fengmk2",
            "email": "fengmk2@gmail.com"
        },
        {
            "name": "dead_horse",
            "email": "dead_horse@qq.com"
        }
    ],
    "name": "co-body",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/cojs/co-body.git"
    },
    "scripts": {
        "test": "make test",
        "test-cov": "make test-cov"
    },
    "version": "5.1.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module co-body](#apidoc.module.co-body)
1.  [function <span class="apidocSignatureSpan">co-body.</span>form (req, opts)](#apidoc.element.co-body.form)
1.  [function <span class="apidocSignatureSpan">co-body.</span>json (req, opts)](#apidoc.element.co-body.json)
1.  [function <span class="apidocSignatureSpan">co-body.</span>text (req, opts)](#apidoc.element.co-body.text)
1.  object <span class="apidocSignatureSpan">co-body.</span>utils

#### [module co-body.utils](#apidoc.module.co-body.utils)
1.  [function <span class="apidocSignatureSpan">co-body.utils.</span>clone (opts)](#apidoc.element.co-body.utils.clone)



# <a name="apidoc.module.co-body"></a>[module co-body](#apidoc.module.co-body)

#### <a name="apidoc.element.co-body.form"></a>[function <span class="apidocSignatureSpan">co-body.</span>form (req, opts)](#apidoc.element.co-body.form)
- description and source-code
```javascript
form = function (req, opts){
  req = req.req || req;
  opts = utils.clone(opts);
  var queryString = opts.queryString || {};

  // keep compatibility with qs@4
  if (queryString.allowDots === undefined) queryString.allowDots = true;

  // defaults
  var len = req.headers['content-length'];
  var encoding = req.headers['content-encoding'] || 'identity';
  if (len && encoding === 'identity') opts.length = ~~len;
  opts.encoding = opts.encoding || 'utf8';
  opts.limit = opts.limit || '56kb';
  opts.qs = opts.qs || qs;

  // raw-body returns a Promise when no callback is specified
  return Promise.resolve()
    .then(function() {
      return raw(inflate(req), opts);
    })
    .then(function(str){
      try {
        var parsed = opts.qs.parse(str, queryString);
        return opts.returnRawBody ? { parsed: parsed, raw: str } : parsed;
      } catch (err) {
        err.status = 400;
        err.body = str;
        throw err;
      }
    });
}
```
- example usage
```shell
...
// application/json
var body = yield parse.json(req);

// explicit limit
var body = yield parse.json(req, { limit: '10kb' });

// application/x-www-form-urlencoded
var body = yield parse.form(req);

// text/plain
var body = yield parse.text(req);

// either
var body = yield parse(req);
...
```

#### <a name="apidoc.element.co-body.json"></a>[function <span class="apidocSignatureSpan">co-body.</span>json (req, opts)](#apidoc.element.co-body.json)
- description and source-code
```javascript
json = function (req, opts){
  req = req.req || req;
  opts = utils.clone(opts);

  // defaults
  var len = req.headers['content-length'];
  var encoding = req.headers['content-encoding'] || 'identity';
  if (len && encoding === 'identity') opts.length = len = ~~len;
  opts.encoding = opts.encoding || 'utf8';
  opts.limit = opts.limit || '1mb';
  var strict = opts.strict !== false;

  // raw-body returns a promise when no callback is specified
  return Promise.resolve()
    .then(function() {
      return raw(inflate(req), opts);
    })
    .then(function(str) {
      try {
        var parsed = parse(str);
        return opts.returnRawBody ? { parsed: parsed, raw: str } : parsed;
      } catch (err) {
        err.status = 400;
        err.body = str;
        throw err;
      }
    });

  function parse(str){
    if (!strict) return str ? JSON.parse(str) : str;
    // strict mode always return object
    if (!str) return {};
    // strict JSON test
    if (!strictJSONReg.test(str)) {
      throw new Error('invalid JSON, only supports object and array');
    }
    return JSON.parse(str);
  }
}
```
- example usage
```shell
...

more options available via [raw-body](https://github.com/stream-utils/raw-body#getrawbodystream-options-callback):

## Example

'''js
// application/json
var body = yield parse.json(req);

// explicit limit
var body = yield parse.json(req, { limit: '10kb' });

// application/x-www-form-urlencoded
var body = yield parse.form(req);
...
```

#### <a name="apidoc.element.co-body.text"></a>[function <span class="apidocSignatureSpan">co-body.</span>text (req, opts)](#apidoc.element.co-body.text)
- description and source-code
```javascript
text = function (req, opts){
  req = req.req || req;
  opts = utils.clone(opts);

  // defaults
  var len = req.headers['content-length'];
  var encoding = req.headers['content-encoding'] || 'identity';
  if (len && encoding === 'identity') opts.length = ~~len;
  opts.encoding = opts.encoding || 'utf8';
  opts.limit = opts.limit || '1mb';

  // raw-body returns a Promise when no callback is specified
  return Promise.resolve()
    .then(function() {
      return raw(inflate(req), opts);
    })
    .then(str => {
      // ensure return the same format with json / form
      return opts.returnRawBody ? { parsed: str, raw: str } : str;
    });
}
```
- example usage
```shell
...
// explicit limit
var body = yield parse.json(req, { limit: '10kb' });

// application/x-www-form-urlencoded
var body = yield parse.form(req);

// text/plain
var body = yield parse.text(req);

// either
var body = yield parse(req);

// custom type
var body = yield parse(req, { textTypes: ['text', 'html'] });
'''
...
```



# <a name="apidoc.module.co-body.utils"></a>[module co-body.utils](#apidoc.module.co-body.utils)

#### <a name="apidoc.element.co-body.utils.clone"></a>[function <span class="apidocSignatureSpan">co-body.utils.</span>clone (opts)](#apidoc.element.co-body.utils.clone)
- description and source-code
```javascript
clone = function (opts) {
  var options = {};
  opts = opts || {};
  for (var key in opts) {
    options[key] = opts[key];
  }
  return options;
}
```
- example usage
```shell
...
 * @param {Options} [opts]
 * @return {Function}
 * @api public
 */

module.exports = function(req, opts){
req = req.req || req;
opts = utils.clone(opts);
var queryString = opts.queryString || {};

// keep compatibility with qs@4
if (queryString.allowDots === undefined) queryString.allowDots = true;

// defaults
var len = req.headers['content-length'];
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
