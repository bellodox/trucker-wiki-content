### Table of Contents

* [[Activation|Nodejs#activation]]
* [[Versions|Nodejs#versions]]
* [[Environment|Nodejs#environment]]
* [[Build behavior|Nodejs#build-behavior]]
* [[Runtime behavior|Nodejs#runtime-behavior]]
* [[Add-ons|Nodejs#add-ons]]

### Activation

The Trucker.io Node.js Support is applied only when the application has a package.json file in the root directory.

### Versions

You can use the `engines` section of your `package.json` to specify the version of Node.js and npm to use on Trucker.io.

```
{
  "name": "exampleapp",
  "version": "0.0.1",
  "engines": {
    "node": "0.10.x",
    "npm":  "1.2.x"
  }
}
```

If you do not specify a version, the latest will be used. To update the Node.js version on an existing application that doesn’t specify a version, just make a trivial commit to your repository and `truck push` your changes.

### Environment

The following environment variables will be set:
`PATH` => `"bin:node_modules/.bin:/usr/local/bin:/usr/bin:/bin"`

### Build behavior

The Node.js buildpack runs the following commands on your app to resolve dependencies:

```
$ npm install --production
$ npm rebuild
```

### Runtime behavior

The highest available version of Node.js that matches your `engines` specification will be available on the `PATH`. You can use it directly in a Procfile:

```
web: node web.js
```

### Add-ons

No add-ons are provisioned by default. If you need a SQL database for your app, add one explicitly:

```
todo
```
