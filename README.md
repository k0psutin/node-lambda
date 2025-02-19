# node-lambda

![node-lambda](../master/node-lambda.png?raw=true)

Command line tool to locally run and deploy your node.js application to [Amazon Lambda](http://aws.amazon.com/lambda/).

![Node CI](https://github.com/motdotla/node-lambda/workflows/Node%20CI/badge.svg)
[![BuildStatus](https://travis-ci.org/motdotla/node-lambda.svg?branch=master)](https://travis-ci.org/motdotla/node-lambda)
[![NPM version](https://badge.fury.io/js/node-lambda.svg)](http://badge.fury.io/js/node-lambda)

```
$ node-lambda run
```

## Installation

```
$ npm install -g node-lambda
```

## Example App

Example apps make it easy to get up and running
* [JavaScript example](https://github.com/motdotla/node-lambda-template).
* [TypeScript example](https://github.com/fogfish/node-lambda-typescript-template).

## Usage

There are 4 available commands.

```
$ node-lambda setup
$ node-lambda run
$ node-lambda package
$ node-lambda deploy
```

### Commands

#### setup

Initializes the `event.json`, `context.json`, `.env`, `deploy.env` files, and `event_sources.json` files. `event.json` is where you mock your event. `context.json` is where you can add additional mock data to the context passed to your lambda function. `.env` is where you place your deployment configuration. `deploy.env` has the same format as `.env`, but is used for holding any environment/config variables that you need to be deployed with your code to Lambda but you don't want in version control (e.g. DB connection info). `event_sources.json` is used to set the event source of the Lambda function (Not all event sources available in Lambda are supported).

```
$ node-lambda setup --help

Usage: setup [options]

Sets up the .env file.


Options:
  -h, --help                        output usage information
  -j, --eventFile [event.json]      Event JSON File
  -x, --contextFile [context.json]  Context JSON File
```

After running setup, it's a good idea to gitignore the generated `event.json` and `.env` files, as well as `.lambda`.

```
$ echo -e ".env\ndeploy.env\nevent.json\n.lambda" >> .gitignore
```

##### Deploy env variables

```
PACKAGE_MANAGER            // (default: 'npm')
AWS_ENVIRONMENT            // (default: '')
AWS_ENDPOINT               // (default: '')
CONFIG_FILE                // (default: '')
EVENT_SOURCE_FILE          // (default: '')
EXCLUDE_GLOBS              // (default: '')
AWS_ACCESS_KEY_ID          // (default: not set!)
AWS_SECRET_ACCESS_KEY      // (default: not set!)
AWS_PROFILE =              // (default: '')
AWS_SESSION_TOKEN =        // (default: '')
AWS_REGION =               // (default: 'us-east-1,us-west-2,eu-west-1')
AWS_FUNCTION_NAME          // (default: package.json.name or 'UnnamedFunction')
AWS_HANDLER                // (default: 'index.handler')
AWS_ROLE_ARN || AWS_ROLE   // (default: 'missing')
AWS_MEMORY_SIZE            // (default: 128)
AWS_TIMEOUT                // (default: 60)
AWS_RUN_TIMEOUT            // (default: 3)
AWS_ARCHITECTURE           // (default: 'x86_64')
AWS_DESCRIPTION            // (default: package.json.description or '')
AWS_RUNTIME                // (default: 'nodejs16.x')
AWS_PUBLISH                // (default: false)
AWS_FUNCTION_VERSION       // (default: '')
AWS_VPC_SUBNETS            // (default: '')
AWS_VPC_SECURITY_GROUPS    // (default: '')
AWS_TRACING_CONFIG         // (default: '')
AWS_LAYERS                 // (default: '')
AWS_LOGS_RETENTION_IN_DAYS // (default: '')
EVENT_FILE                 // (default: 'event.json')
PACKAGE_DIRECTORY          // (default: not set)
CONTEXT_FILE               // (default: 'context.json')
PREBUILT_DIRECTORY         // (default: '')
SRC_DIRECTORY              // (default: '')
DEPLOY_TIMEOUT             // (default: '120000')
DOCKER_IMAGE               // (default: '')
DEPLOY_ZIPFILE             // (default: '')
DEPLOY_USE_S3              // (default: false)
IMAGE_URI                  // (default: '')
AWS_DLQ_TARGET_ARN         // (default: not set)
AWS_TAGS                   // (default: '')
```

#### run

Runs your Amazon Lambda index.js file locally. Passes `event.json` data to the Amazon Lambda event object.

```
$ node-lambda run --help
Usage: node-lambda run|execute [options]

Run your Amazon Lambda application locally

Options:
  -H, --handler [AWS_HANDLER]                                 Lambda Handler {index.handler} (default: "index.handler")
  -j, --eventFile [EVENT_FILE]                                Event JSON File (default: "event.json")
  -u, --runtime [AWS_RUNTIME]                                 Lambda Runtime (default: "nodejs16.x")
  -t, --timeout [AWS_RUN_TIMEOUT]                             Lambda Timeout (default: 3)
  -f, --configFile [CONFIG_FILE]                              Path to file holding secret environment variables (e.g. "deploy.env") (default: "")
  -x, --contextFile [CONTEXT_FILE]                            Context JSON File (default: "context.json")
  -M, --enableRunMultipleEvents [ENABLE_RUN_MULTIPLE_EVENTS]  Enable run multiple events (default: true)
  -y, --proxy [PROXY]                                         Proxy server (default: "")
  --apiGateway                                                Convert to API Gateway events (default: false)
  -h, --help                                                  display help for command
```

#### package

Bundles your application into a local zip file.

```
$ node-lambda package --help
Usage: node-lambda package|zip [options]

Create zipped package for Amazon Lambda deployment

Options:
  --packageManager [PACKAGE_MANAGER]            Package manager used to install dependencies (default: "npm", options: "npm", "yarn")
  -A, --packageDirectory [PACKAGE_DIRECTORY]    Local Package Directory
  -I, --dockerImage [DOCKER_IMAGE]              Docker image for npm ci (default: "")
  -n, --functionName [AWS_FUNCTION_NAME]        Lambda FunctionName (default: "node-lambda")
  -H, --handler [AWS_HANDLER]                   Lambda Handler {index.handler} (default: "index.handler")
  -e, --environment [AWS_ENVIRONMENT]           Choose environment {dev, staging, production} (default: "")
  -x, --excludeGlobs [EXCLUDE_GLOBS]            Space-separated glob pattern(s) for additional exclude files (e.g.
                                                "event.json dotenv.sample") (default: "")
  -D, --prebuiltDirectory [PREBUILT_DIRECTORY]  Prebuilt directory (default: "")
  -m, --keepNodeModules [KEEP_NODE_MODULES]     Keep the current node_modules directory. (default: false)
  -v, --dockerVolumes [DOCKER_VOLUMES]          Additional docker volumes to mount. Each volume definition has to be
                                                separated by a space (e.g. "$HOME/.gitconfig:/etc/gitconfig
                                                $HOME/.ssh:/root/.ssh") (default: "")
  --no-optionalDependencies                     Run `npm install` with `--no-optional`
  -h, --help                                    display help for command
```

#### deploy

Bundles and deploys your application up to Amazon Lambda.

```
$ node-lambda deploy --help
Usage: node-lambda deploy [options]

Deploy your application to Amazon Lambda

Options:
  --packageManager [PACKAGE_MANAGER]                    Package manager used to install dependencies (default: "npm", options: "npm", "yarn")
  -e, --environment [AWS_ENVIRONMENT]                   Choose environment {dev, staging, production} (default: "")
  -E, --endpoint [AWS_ENDPOINT]                         Choose endpoint (e.g. localstack, "http://127.0.0.1:4574") (default: "")
  -a, --accessKey [AWS_ACCESS_KEY_ID]                   AWS Access Key
  -s, --secretKey [AWS_SECRET_ACCESS_KEY]               AWS Secret Key
  -P, --profile [AWS_PROFILE]                           AWS Profile (default: "")
  -k, --sessionToken [AWS_SESSION_TOKEN]                AWS Session Token (default: "")
  -r, --region [AWS_REGION]                             AWS Region (default: "us-east-1,us-west-2,eu-west-1")
  -n, --functionName [AWS_FUNCTION_NAME]                Lambda FunctionName (default: "node-lambda")
  -H, --handler [AWS_HANDLER]                           Lambda Handler {index.handler} (default: "index.handler")
  -o, --role [AWS_ROLE]                                 Amazon Role ARN (default: "missing")
  -m, --memorySize [AWS_MEMORY_SIZE]                    Lambda Memory Size (default: 128)
  -t, --timeout [AWS_TIMEOUT]                           Lambda Timeout (default: 60)
  --architecture [AWS_ARCHITECTURE]                     The instruction set architecture that the function supports.  (x86_64|arm64) (default: "x86_64")
  -d, --description [AWS_DESCRIPTION]                   Lambda Description (default: "Command line tool for locally running and remotely deploying your node.js applications to Amazon Lambda.")
  -u, --runtime [AWS_RUNTIME]                           Lambda Runtime (default: "nodejs16.x")
  -p, --publish [AWS_PUBLISH]                           Lambda Publish (default: false)
  -L, --lambdaVersion [AWS_FUNCTION_VERSION]            Lambda Function Version (default: "")
  -b, --vpcSubnets [AWS_VPC_SUBNETS]                    Lambda Function VPC Subnet IDs (comma delimited) (default: "")
  -g, --vpcSecurityGroups [AWS_VPC_SECURITY_GROUPS]     Lambda VPC Security Group IDs (comma delimited) (default: "")
  -K, --kmsKeyArn [AWS_KMS_KEY_ARN]                     Lambda KMS Key ARN (default: "")
  -Q, --deadLetterConfigTargetArn [AWS_DLQ_TARGET_ARN]  Lambda DLQ resource
  -c, --tracingConfig [AWS_TRACING_CONFIG]              Lambda tracing settings (default: "")
  -l, --layers [AWS_LAYERS]                             Lambda Layers settings (e.g. "ARN1,ARN2[,..])" (default: "")
  -R, --retentionInDays [AWS_LOGS_RETENTION_IN_DAYS]    CloudWatchLogs retentionInDays settings (default: "")
  -G, --sourceDirectory [SRC_DIRECTORY]                 Path to lambda source Directory (e.g. "./some-lambda") (default: "")
  -I, --dockerImage [DOCKER_IMAGE]                      Docker image for npm ci (default: "")
  -f, --configFile [CONFIG_FILE]                        Path to file holding secret environment variables (e.g. "deploy.env") (default: "")
  -S, --eventSourceFile [EVENT_SOURCE_FILE]             Path to file holding event source mapping variables (e.g. "event_sources.json") (default: "")
  -x, --excludeGlobs [EXCLUDE_GLOBS]                    Space-separated glob pattern(s) for additional exclude files (e.g. "event.json dotenv.sample") (default: "")
  -D, --prebuiltDirectory [PREBUILT_DIRECTORY]          Prebuilt directory (default: "")
  -T, --deployTimeout [DEPLOY_TIMEOUT]                  Deploy Timeout (default: 120000)
  -z, --deployZipfile [DEPLOY_ZIPFILE]                  Deploy zipfile (default: "")
  -B, --deployUseS3 [DEPLOY_USE_S3]                     Use S3 to deploy. (default: false)
  -i, --imageUri [IMAGE_URI]                            URI of a container image in the Amazon ECR registry. (default: "")
  -y, --proxy [PROXY]                                   Proxy server (default: "")
  -A, --tags [AWS_TAGS]                                 Tags as key value pairs (e.g. "tagname1=tagvalue1,tagname2=tagvalue2)" (default: "")
  --silent                                              Silent  or  quiet mode (default: false)
  --no-optionalDependencies                             Run `npm install` with `--no-optional`
  -h, --help                                            display help for command
```

If you are deploying to a custom endpoint you may also need to pass in an access key/secret. For localstack these can be anything, but cannot be blank:

```
node-lambda deploy --endpoint http://localhost:4574 --accessKey '1234' --secretKey '1234'
```

## Custom Environment Variables

AWS Lambda will let you set environment variables for your function. Use the sample `deploy.env` file in combination with the `--configFile` flag to set values which will be added to the lambda configuration upon deploy.  Environment variables will also be set when running locally using the same flag

## Node.js Runtime Configuration

AWS Lambda now supports Node.js 16 and Node.js 14. Please also check the [Lambda runtimes](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html) page.

## Use S3 to deploy

Use the command line argument `--deployUseS3` or `-B`.  (This option is true/false.)
Example: `% node-lambda deploy -B`

You can also set the environment variable `DEPLOY_USE_S3`.
Example: `DEPLOY_USE_S3=true`

Use the environment variable to set the bucket name or S3 key prefix.
The environment variable name is different for each region. Please set it to the environment variable you want to deploy.
It can be set in `.env`.

Example:

```sh
# S3_<region>_BUCKET
S3_US_WEST_1_BUCKET=test_aaa

# S3_<region>_PREFIX
S3_US_WEST_1_PREFIX=bbb
```

In this case, the S3 key:
`test_aaa/bbb/deploy-package-${FunctionName}.zip`

## To deploy a container image to Lambda

1. [Pushing a Docker image to ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html)
2. Specify the Image URI of the ECR with the environment variable:`IMAGE_URI` or `--imageUri` option, and deploy.

## Post install script
When running `node-lambda deploy` if you need to do some action after `npm install --production` and before deploying to AWS Lambda (e.g. replace some modules with precompiled ones or download some libraries, replace some config file depending on environment) you can create `post_install.sh` script. If the file exists the script will be executed (and output shown after execution) if not it is skipped. Environment string is passed to script as first parameter so you can use it if needed. Make sure that the script is executable.

Example `post_install.sh`:
```
printf "\n\n######  Post install script  ###### \n"
ENV="production";
if [ ! -z $1 ]
  then
    ENV=$1;
fi
cp -v "config_$ENV.js" "config.js" \
&& printf "######  DONE!  ###### \n\n"
```

## Post invoke script (example)
If you wish to invoke your deployed AWS Lambda function, you can add the following as a `script` to your `package.json`:

```
"invoke:remote": "aws lambda invoke --function-name myLambdaFnName --payload fileb://fixtures/hi.json invoked.json --log-type Tail | jq -r '.LogResult' | base64 --decode && rm invoked.json"
```

## Prebuilt packages
The `--prebuiltDirectory` flag is useful for working with Webpack for example. It skips `npm install --production` and `post_install.sh` and simply packages the specified directory.

## Handling `npm link` and Dependencies With Local Paths
Perhaps the easiest way to handle these cases is to bundle the code using Webpack and use the `--prebuiltDirectory` flag to package the output for deployment.

## ScheduleEvents
#### Optional Parameter
When using the eventSourceFile flag (-S or --eventSourceFile) to set a ScheduleEvent trigger, you can pass an optional _ScheduleDescription_ key into the ScheduleEvent object with a custom description for the CloudWatch event rule you are defining. By default, node-lambda generates a _ScheduleDescription_ for you based on the ScheduleName and ScheduleExpression of the rule.

#### Note on ScheduleState for ScheduleEvents
When setting ScheduleState to `ENABLED` or `DISABLED` for ScheduleEvents, it is useful to note that this sets the state of the CloudWatch Event rule but _DOES NOT_ set the state of the trigger for the Lambda function you are deploying; ScheduleEvent triggers are enabled by default in the Lambda console when added using the eventSourceFile flag.

#### Known Issue
###### Duplicate ScheduleEvent Triggers
If you are adding a trigger via the `eventSourceFile` for the first time, remove preexisting triggers from the Lambda console before deploying. Deploying a Lambda with the `--eventSourceFile` flag will *NOT* overwrite the same triggers created from the AWS console and may result in a duplicate triggers for the same rule.

## Other AWS Lambda Tools Projects

+ [lambdaws](https://github.com/mentum/lambdaws)
+ [lambdaws-instant-api](https://github.com/mentum/lambdaws-instant-api)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

### Coding style
This project uses [JavaScript Standard Style](https://standardjs.com/).

[![JavaScript Style Guide](https://cdn.rawgit.com/feross/standard/master/badge.svg)](https://github.com/feross/standard)

## Running tests

```
$ npm install
$ npm test
```
