# TERRA 10 Serverless labs

## Lab 001 - Your first Serverless project

## Boiler up your project
run:
``` 
npm install serverless -g
serverless create --template aws-nodejs-typescript
npm install
```
What happened? This created a boilerplate Serverless application that is technically ready to be deployed. 

Lets check the result:
* handler.ts
* package.json
* package-lock.json
* serverless.yml
* source-map-install.js
* tsconfig.json
* webpack.config.js

## serverless.yml
The start of your serverless configuration. 
Inspect:
* _service / name_ holds our unique project name which reflects everywhere including our AWS CloudFormation stack
* _functions_ contains our Lambda functions where we get a free HelloWorld examples
* _plugins_ contains the additional (open source) plugins for the Serverless framework

Actions:
* change name from _aws-nodejs-typescript_ to _t10*-serverless_ with an unique ID
* add the following elements to provider section:
``` 
provider:
  name: aws
  runtime: nodejs8.10
  region: eu-central-1
  stage: dev
  versionFunctions: false
  logRetentionInDays: 5
  deploymentBucket:
    name: serverlessdeployment.${self:provider.region}.terra10.io
```
This will:
* deploy to the correct AWS region
* define our stage, which is DEV by default
* disable versioning (otherwise we keep old versions of our Lambdas) 
* make sure we don't store the (CloudWatch) logging forever
* configure a fixed S3 bucket for the Serverless deployments

## package.json
The node/npm configuration for your package dev, build and deploy. Check the _dependencies_ and _devDependencies_ sections. They list the packages used for development and runtime.

## package-lock.json
Never edit, never touch this file, go away! Just check in after npm install and be afraid!

## handler.ts
Our first generated function doing nothing much, but enjoy it and if you are brave, try changing the message result.

## Let's roll
Run and check the result:
``` 
serverless invoke local --function hello
```
Explanation:
* We use the serverless framework and webpack plugin to execute the typescript function locally.

## Finally
We don't want all the functions in our root folder, so let's:
* create a folder `/src`
* move _handler.ts_ to _/src/handler.ts_

Since we are going to use TypeScript we want tslint for nice code, so:
* run: `npm install tslint --save-dev`
* create _/tslint.json_ and copy the contents from _/Lab001/references/tslint.json_

The default tsconfig.json is a bit minimalistic, so:
* replace /tsconfig.json with the file from /Lab001/references

We changed the output dir in tsconfig.json to /lib so we have to make sure to adjust the serverless configuration so:
* open _serverless.yml_
* change the hello function handler to : `lib/handler.hello`

Let's run Typescript compile to check if our code is decent:
* run: `tsc`

We might see an error which shows the example template is not really clean code. The handler function, which is the entry from API Gateway to the AWS Lambda function, contains a variable context which is never used. Our new tsconfig.json does not allow this so: 
* add `_` in front of the word `context`, making the result like this:
``` 
export const hello: Handler = (event: APIGatewayEvent, _context: Context, cb: Callback) => {
```
* run: `tsc`
* check the /lib folder to see the compiled Javascript result which in turn will be used by the Serverless framework.

Result/Summary:
* We now have a Typescript lambda function
* Which compiles with `tsc` to basic Javascript in /lib
* Which in turn is used by serverless.yml


