# Overview

[Typescript](https://www.typescriptlang.org/) Firebase [Cloud Functions](https://firebase.google.com/docs/functions/) for invitations, registration workflows, etc. Also has integrations with third party services (such as hubspot and mailchimp/mandrill) in order to support the workflows.

## Authentication

For endpoints that require authentication, the [authenticatedRequest](functions/src/util/authenticateRequest) function checks for a valid `Authorization: Bearer` header in the request. The bearer token is the JWT issued by the firebase authentication library.

## API Documentation.

API documentation is written using [OpenAPI 3.0](https://swagger.io/blog/announcing-openapi-3-0/). A UI version of the API docs can be viewed in the browser at <http://localhost:3000> after running `npm run api-docs` (first run `npm install` if you haven't already).


# Local Development

## Prerequisites

You must have `node.js` installed. Currently (Jan 2018), [Firebase supports version 6.11.5](https://firebase.google.com/docs/functions/get-started) of Node.js. You likely want to use `nvm` to manage versions:

    $ brew install nvm # make sure to follow setup instructions
    $ nvm
    $ nvm install 6.11.5

 The version of npm with 6.11.5 is ancient, so update npm to a new version that works properly with package-lock.json.

    $ npm install -g npm

Now you're ready to develop. go to `functions` and run:

    $ npm install

## Testing

All code is under the `functions` directory. To execute tests, go to that directory and run:

    $ npm test

This runs `tslint` before any unit tests.

### Integration Testing

Unit tests can be run with:

    $ npm run integration

There are a few caveats:

1. Unit tests talk to the `studioplatform-dev` account. You must be logged into that account locally.
1. Ensure that the latest version of the endpoints is deployed. Use: `npm run deploy`. Some of the code to verify a user status in firebase, mailchimp, etc, runs locally.
1. The tests are not meant to be run by two users simultaneously.

If you get the following error: `Error: Could not load the default credentials. Browse to https://developers.google.com/accounts/docs/application-default-credentials for more information.`

Try:

    $ cd ~/.config/gcloud
    $ rm application_default_credentials.json
    $ gcloud auth application-default login


## Linting and Unit tests

For unit testing, we use [mocha](https://mochajs.org/) and [chai](http://chaijs.com/). To support unit tests written in Typescript, mocha runs with [ts-node](https://github.com/TypeStrong/ts-node). See https://journal.artfuldev.com/unit-testing-node-applications-with-typescript-using-mocha-and-chai-384ef05f32b2 for details.

For linting, we use [TSLint](https://palantir.github.io/tslint/).

## Firebase Setup

Install the firebase tools:

    $ npm install -g firebase-tools

Note: there was a bug in version [3.17.3]( https://stackoverflow.com/questions/48490892/access-of-configuration-variable-in-cloud-functions-firebase-cli-local-emulator) so ensure you're on a newer version.

Authenticate (one-time) with firebase:

    $ firebase login

Then, select the project to use:

    $ firebase use --add
    Which project do you want to add? studiosplatform
    What alias do you want to use for this project? (e.g. staging) prod

Note: currently we don't need the [admin credentials for emulated functions](https://firebase.google.com/docs/functions/local-emulator#set_up_admin_credentials_optional). All tests and scripts use your credentials from `firebase login`.

## Firebase Local Dev

Firebase includes a local shell and [an emulator](https://firebase.google.com/docs/functions/local-emulator) that can be used to test and run locally.

All commands in this section should be run within the `functions` directory.

Local shell and web services populate `firebase.config()` from `functions/.runtimeconfig.json`. You can match this file to production with.

    $ firebase functions:config:get > .runtimeconfig.json

To run the shell:

    $ npm run shell

If you see an error like: `⚠  functions: Cannot start emulator. Error: Module version mismatch. Expected 48, got 57.` ensure you have the right version of node installed.

To run a local web service with the firebase functions:

    $ npm run serve

# External Dependencies

* Google Firebase
* Hubspot - [API Docs](https://developers.hubspot.com/)

# Configuration Settings

Secrets, such as API keys, are stored in the [firebase functions config](https://firebase.google.com/docs/functions/config-env#access_environment_configuration_in_a_function). To find the current values, run:

    $ firebase functions:config:get

To add a new setting, like a new API key:

    $ firebase functions:config:set someservice.key="THE API KEY"

Inside the code, reference it like:

    functions.config().someservice.id

## Required configs:

* `mailchimp.apikey`
* `mailchimp.listid`
* `hubspot.apikey` - This is the "API Key" from the Integrations section, e.g.:
   https://app.hubspot.com/integrations-beta/4235667/your-integrations/api-key
* `hubspot.clientsecret` - this is the "Client Secret" from the developer application, e.g.:
   https://app.hubspot.com/developers-alpha/4236338/application/59806

# Deployment

We have two environments:

* studiosplatform-dev (dev)
* studiosplatform (prod)

You can configure aliases for these with `firebase use --add`.


**__Warning!__**: Don't deploy functions directly using `firebase deploy --only:functions` as this will remove any functions not in this project (i.e. those from the [studios_chatbot_backend](https://github.com/StudiosAtBetaworks/studios_chatbot_backend) project).

Instead, use `npm run deploy`

# Known issues

* Must specify `GCLOUD_PROJECT` env var when running unit tests. See: https://github.com/firebase/firebase-functions/issues/157 and package.json `test` definition.
* NYC for code coverage breaks line numbers in unit test tracebacks. See: https://github.com/istanbuljs/nyc/issues/618
"# todoListApi" 
