# json-schema-deref-sync

Dereference JSON pointers in a JSON schemas with their true resolved values.
Basically a lighter, synchronous version of [json-schema-deref](https://github.com/bojand/json-schema-deref) but omits web references and
custom loaders.

## Installation

`npm install json-schema-deref-sync`

## Overview

Let's say you have the following JSON Schema:

```json
{
  "description": "Just some JSON schema.",
  "title": "Basic Widget",
  "type": "object",
  "definitions": {
    "id": {
      "description": "unique identifier",
      "type": "string",
      "minLength": 1,
      "readOnly": true
    }
  },
  "properties": {
  "id": {
    "$ref": "#/definitions/id"
  },
  "foo": {
    "$ref": "http://www.mysite.com/myschema.json#/definitions/foo"
  },
  "bar": {
    "$ref": "bar.json"
  }
}
```

Sometimes you just want that schema to be fully expanded, with `$ref`'s being their (true) resolved values:

```json
{
  "description": "Just some JSON schema.",
  "title": "Basic Widget",
  "type": "object",
  "definitions": {
    "id": {
      "description": "unique identifier",
      "type": "string",
      "minLength": 1,
      "readOnly": true
    }
  },
  "properties": {
    "id": {
      "description": "unique identifier",
      "type": "string",
      "minLength": 1,
      "readOnly": true
    },
    "foo": {
      "description": "foo property",
      "readOnly": true,
      "type": "number"
    },
    "bar": {
      "description": "bar property",
      "type": "boolean"
    }
  }
}
```

This utility lets you do that:


```js
var deref = require('json-schema-deref-sync');
var myschema = require('schema.json');

var fullSchema = deref(myschema);
```

## API

### deref(schema, options)

Dereferences `$ref`'s in json schema to actual resolved values. Supports local, and file refs.

If circular references are found it returns an instance of `Error`.

Parameters:

##### `schema`
The input JSON schema

##### `options`

- `baseFolder` - the base folder to get relative path files from. Default is `process.cwd()`

- `keepLocalRefs` - Whether local `$ref`s like `"$ref": "#/definitions/foo"` should not dereferenced. Passing true will pull all `definitions` to the root of the resulting schema. Default is `false`

### deref.getRefPathValue(schema, refPath)

Gets the "local" ref value given the path.

`schema` - the (root) json schema to search

`refPath` - string ref path to get within the schema. Ex. `#/definitions/id`

```js
var localValue = deref.getRefPathValue(myschema, '#/definitions/foo');
console.dir(localValue);
```
