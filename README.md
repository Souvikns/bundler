[![AsyncAPI Bundler](./assets/logo.png)](https://www.asyncapi.com)

[![Github license](https://img.shields.io/github/license/asyncapi/bundler)](https://github.com/asyncapi/bundler/blob/main/LICENSE)
[![PR testing - if Node project](https://github.com/asyncapi/bundler/actions/workflows/if-nodejs-pr-testing.yml/badge.svg)](https://github.com/asyncapi/bundler/actions/workflows/if-nodejs-pr-testing.yml)
[![npm](https://img.shields.io/npm/dw/@asyncapi/bundler)](https://www.npmjs.com/package/@asyncapi/bundler)

<!-- toc is generated with GitHub Actions do not remove toc markers  -->

<!-- toc -->

- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)
- [bundle(files, options)](#bundlefiles-options)

<!-- tocstop -->

## Overview 
An official library that lets you bundle/merge your specification files into one. AsyncAPI bundler can help you if - 

<details>
<summary>your specification file is divided into different smaller files and is using json `$ref` to reference components </summary>

```yaml

# asyncapi.yaml
asyncapi: '2.2.0'
info:
  title: Account Service
  version: 1.0.0
  description: This service is in charge of processing user signups
channels:
  user/signup:
    subscribe:
      message:
        $ref: './messages.yaml#/messages/UserSignedUp'

#messages.yaml
messages:
  UserSignedUp:
    payload:
      type: object
      properties:
        displayName:
          type: string
          description: Name of the user
        email:
          type: string
          format: email
          description: Email of the user

# After combining 
asyncapi: 2.2.0
info:
  title: Account Service
  version: 1.0.0
  description: This service is in charge of processing user signups
channels:
  user/signedup:
    subscribe:
      message:
        payload:
          type: object
          properties:
            displayName:
              type: string
              description: Name of the user
            email:
              type: string
              format: email
              description: Email of the user

```

</details>

<details>
<summary>you have different standalone specification files that define a larger system, see examples here </summary>

```yaml

# signup.yaml 
asyncapi: '2.2.0'
info:
  title: Account Service
  version: 1.0.0
  description: This service is in charge of processing user Signup 

channels:
  user/signedup:
    subscribe:
      message:
        payload:
          type: object
          properties:
            displayName:
              type: string
            email:
              type: string
              format: email


# login.yaml
asyncapi: '2.2.0'
info:
  title: Account Service
  version: 1.0.0
  description: This service is in charge of processing user signup

channels:
  user/loggenin:
    subscribe:
      message:
        payload:
          type: object
          properties:
            displayName:
              type: string

# After combining 
# asyncapi.yaml 
asyncapi: '2.2.0'
info:
  title: Account Service
  version: 1.0.0
  description: This service is in charge for processing user authentication 

channles:
  user/signedup:
    subscribe:
      message:
        payload:
          type: object
          properties:
            displayName:
              type: string
            email:
              type: string
              format: email
  user/loggedin:
    subscribe:
      message:
        payload:
          type: object
          properties:
            displayName:
              type: string
```

</details>

<br>

## Installation 

```
npm install @asyncapi/bundler
```

## Usage 

AsyncAPI-bundler could be easily used within your javascript projects as a Nodejs module. 

```js
const bundler = require('@asyncapi/bundler');
const fs = require('fs');
const path = require('path');

const filePaths = ['./camera.yml','./audio.yml']
const document = await bundler.bundle(
  filePaths.map(filePath => fs.readFileSync(path.resolve(filePaths), 'utf-8')),
  {
    base: fs.readFileSync(path.resolve('./base.yml'), 'utf-8')
  }
);

console.log(document.json()); // the complete bundled asyncapi document.
```

<a name="bundle"></a>

## bundle(files, options)
**Kind**: global function  

| Param | Type | Description |
| --- | --- | --- |
| files | <code>Array.&lt;string&gt;</code> \| <code>Array.&lt;Object&gt;</code> | files that are to be bundled |
| options | <code>Object</code> |  |
| options.base | <code>string</code> \| <code>object</code> | base object whose prperties will be retained. |
| options.parser | <code>Object</code> | asyncapi parser object |
| options.validate | <code>boolean</code> | pass false to not validate file before merge |
