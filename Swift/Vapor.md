# Vapor

## Commands

Swift
* `swift build`

Vapor
* `vapor --help`

## Steps

### Vapor Toolbox

Get latest Vapor CLI. In terminal do the following:
* Run `brew update`
* Run `brew upgrade vapor` to pull down latest version of the toolbox

Alternatively can run `brew update brew upgrade` to update Homebrew and upgrade all projects

More information in the [Homebrew documentation](https://docs.brew.sh/FAQ) and [Homebrew cheatsheet](https://devhints.io/homebrew)

### Create and Setup New Project

In Terminal do the following:
* Go the location I want the project to live in.
* Create new project with `vapor new <name>`
* Follow fluent prompts if using database.
* Change directory into the project with `cd <name>`
* Delete unnecessary files with
```
rm -rf Sources/App/Models/*
rm -rf Sources/App/Migrations/*
rm -rf Sources/App/Controllers/*
```
* Create `.env` file with `touch .env`
* Open project in xcode with `vapor xcode`
* Delete `try app.register(collection: TodoController())` from `routes.swift`
* Delete `app.migrations.add(CreateTodo())` from `configure.swift`
* Comment out `app.databases.use()` from `configure.swift`
* Wait for the packages to fetch and check out
* Set custom directory in Opetions tab in menu Product > Scheme > Edit Scheme
* Add the following as a pre-action script in pre-action in the run process in Edit Scheme, in the disclosure triange of Run on the sidebar `lsof -i :8080 -sTCP:LISTEN |awk 'NR > 1 {print $2}'|xargs kill -15`
* Create `.env` file in project root
* Add auto migrate `try app.autoMigrate().wait()` in configure.swift

See more advice in [The Swift Dev Vapor 4 advice article](https://theswiftdev.com/10-short-advices-that-will-make-you-a-better-vapor-developer-right-away/)


### Test Project

Do the following in Terminal from the project directory:
* Run project
* Run tests
* Build project `docker build -t melo-api:1.0 .` or `docker build -t <NAME> .`
* Run the project `docker run -it -p 8080:8080 melo-api:1.0` or `docker run -it -p 8080:8080 <NAME>`


More information on the docker test in [Christian Weinberger’s article](https://medium.com/@cweinberger/serverless-server-side-swift-using-google-cloud-run-2b314ce74293) from 27 May 2019.

More information in [Docker documentation](https://docs.docker.com/get-started/part2/)


### Build Project

Docker build doesn't include anything in .gitignore so need to comment out the files from that before building if you want them in the project
* `docker build -t melo-api:1.0 .`

### Setup gcloud CLI

* [Sign in to gcloud cli](https://cloud.google.com/sdk/gcloud/reference/auth/login)

### Setup Google SQL

* In console create a Cloud SQL instance
* Name it
* Add or generate a password (save it)
* Select region
* Datavase version PostgreSQL 12
* (Optional) Add `0.0.0.0/0` public IP to Connectivity, name it all
* Change `Automated backups` window to `01:00 - 05:00`
* 1 vCPU
* 3.75 GB
* 10GB SSD
* Wait for instance to build and copy details and insert into `.env` and any other files with secrets

For migrations (optional)
* Access previous project
* Select SQL instance and on the Overview tab click export and export to a Storage container (create one if needed)
* Download from Storage container
* Create Storage container in new account
* Upload database
* Select SQL instance and on the Overview tab click import and select uploaded filed in the new Storage container
* [Exporting document](https://cloud.google.com/sql/docs/postgres/import-export/exporting)
* [Importing document](https://cloud.google.com/sql/docs/postgres/import-export/importing)

### Deploy Project to Google Cloud Run

By default, .gcloudignore include the rules of .gitignore as well. https://cloud.google.com/sdk/gcloud/reference/topic/gcloudignore

Do the following in Terminal from the project directory:
* Read all the projects `gcloud projects list` and remember the `NAME` or `PROJECT_ID` of the project
* Change the project with `gcloud config set project proven-metric-301007` or `gcloud config set project <PROJECT_ID>`
* Submit the with `gcloud builds submit --tag gcr.io/proven-metric-301007/melo-api:1.0 --timeout="30m"` or `gcloud builds submit --tag gcr.io/<PROJECT_ID>/<CONTAINER_NAME> --timeout="30m"`
* Deploy and run with `gcloud run deploy api-v1 --image gcr.io/proven-metric-301007/melo-api:1.0 --platform managed --allow-unauthenticated` or `gcloud run deploy <INSTANCE_NAME> --image gcr.io/<PROJECT_ID>/<CONTAINER_NAME> --platform managed --allow-unauthenticated`

More information on the in [Grant Timmerman’s articles](https://medium.com/google-cloud/swift-on-cloud-run-b6397a428d) from 1 July 2020 and [Christian Weinberger’s article](https://medium.com/@cweinberger/serverless-server-side-swift-using-google-cloud-run-2b314ce74293) from 27 May 2019.

More information in [Google Cloud Run building documentation](https://cloud.google.com/run/docs/building/containers) and [Google Cloud Run deploy documentation](https://cloud.google.com/run/docs/deploying)

### Setting up SQL Connection

#### Google Cloud
* [Connecting from Cloud Run (fully managed) to Cloud SQL](https://cloud.google.com/sql/docs/mysql/connect-run)
* [Quickstart for using the proxy for local testing](https://cloud.google.com/sql/docs/mysql/quickstart-proxy-test)
* [Troubleshooting SSL certificates - Domain status](https://cloud.google.com/load-balancing/docs/ssl-certificates/troubleshooting#domain-status)
* [Using Google-managed SSL certificates](https://cloud.google.com/load-balancing/docs/ssl-certificates/google-managed-certs#update-dns)

### Setting Up Environment Variables for Prouduction

#### Environment Variable setup

Some usefule resources are:
* [Vapor 4 Environment documentation](https://docs.vapor.codes/4.0/environment/)
* [How to store keys in env files?](https://theswiftdev.com/how-to-store-keys-in-env-files/)

#### Docker

Some useful resources are:
* [Docker ARG vs ENV](https://vsupalov.com/docker-arg-vs-env/)
* [Docker ARG, ENV and .env - a Complete Guide](https://vsupalov.com/docker-arg-env-variable-guide/)
* [Docker Compose environment variables](https://docs.docker.com/compose/environment-variables/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
* [Nodes Agency Dockerfile examples](https://github.com/nodes-vapor/dockerfiles)

#### Google Cloud
Some useful resources are:
* [Using Environment Variables in Run](https://cloud.google.com/run/docs/configuring/environment-variables)


### Custom Domain

To map a custom domain in Google Cloud Run I needed to set up the instance in America as it is not available in Australia.
Read more in [Mapping custom domains](https://cloud.google.com/run/docs/mapping-custom-domains) document.

Alternatively I can set up a cloud load balancer.


### Google Cloud Load Balancing

* [Setting up a load balancer with Cloud Run (fully managed), App Engine, or Cloud Functions](https://cloud.google.com/load-balancing/docs/https/setting-up-https-serverless)

### Google Cloud SSL Certificates

* [SSL certificates overview](https://cloud.google.com/load-balancing/docs/ssl-certificates)
* [Using Google-managed SSL certificates](https://cloud.google.com/load-balancing/docs/ssl-certificates/google-managed-certs)

### Google Cloud App Engine

* [App Engine](https://cloud.google.com/appengine)
* [Mapping Custom Domains](https://cloud.google.com/appengine/docs/standard/go/mapping-custom-domains)

### Google Cloud DNS

* [Cloud DNS](https://cloud.google.com/dns/docs)
* Change Name Servers in [GoDaddy](https://dcc.godaddy.com/) or other registar
* [Flush Cache](https://developers.google.com/speed/public-dns/cache) or [clear dns](https://kinsta.com/knowledgebase/clear-dns-cache/) or [purge cache]() if needed
* [Check cache](https://cachecheck.opendns.com/)

## Debugging

To debug in Google Cloud Run go to Logging under Operations. You can read more about [logging](https://cloud.google.com/run/docs/logging) and [troubleshooting Run](https://cloud.google.com/run/docs/troubleshooting).


## Status and Info

[Google Cloud Regions](https://cloud.google.com/about/locations)
[Google Cloud Status](https://status.cloud.google.com/)


## CI/CD

The following resources are for Google Cloud Run:
* [Creating a CI/CD Environment for Serverless Containers on Google Cloud Run with GitHub Actions - 3 October 2020](https://medium.com/google-cloud/creating-a-ci-cd-environment-for-serverless-containers-on-google-cloud-run-with-github-actions-7492ca3993a0)
* [How to Set Up a Deployment Pipeline on Google Cloud with Cloud Build, Container Registry and Cloud Run - 18 May 2020](https://medium.com/ci-t/how-to-set-up-a-deployment-pipeline-on-gcp-with-cloud-build-container-registry-and-cloud-run-73391f5b77e4)
* [https://medium.com/google-cloud/simplifying-continuous-deployment-to-cloud-run-with-cloud-build-including-custom-domain-setup-ssl-22d23bed5cd6 - 14 April 2019](https://medium.com/google-cloud/simplifying-continuous-deployment-to-cloud-run-with-cloud-build-including-custom-domain-setup-ssl-22d23bed5cd6)


## Environment

Environment variables are loaded into an Environment extension in a file named `Environment+app.swift` in the `Extensions` folder. They utilise variables from `.env` the Dockerfile. These variables are force unwrapped so will crash the app if they aren't there.

## Authentication

This application uses JWT along with Cookies (web) and Authoization Headers (Bearer, api and app).
* Go to https://mkjwk.org/ and create a new key pair. Select 2048 as Key Size, Signing/Signature as Key Use, RS256 as Algorithm, and backend as the Key ID. You must put something in the Key ID or it will fail
* Store the content under **Public and Private Keypair set** in a file within your project directory and call it keypair.jwks. **DO NOT PUBLISH THIS FILE**.
* Add the following to the `.env` file in the project directory
```
JWKS=<COPY_THE_KEYPAIR_SET_AS_ONE_LINE_HERE>
```
* Add the JSON Web Key Set (JWKS) to Environment extension in `Enironment+App`

I had some difficulty with this as it is important that each key have an identifier (kid, Key ID), if you don't have a identifier then JWKit will through an error saying invalid key.

Another way to set this up is to create a key with some variables and then add that directly.

```
// configure.swift

// Set up JSON Web Key and use as signer
// let key = JWK.rsa(
//   .rs256,
//   identifier: JWKIdentifier(string: Environment.JWKS.keyID),
//   modulus: Environment.JWKS.modulus,
//   exponent: Environment.JWKS.exponent,
//   privateExponent:Environment.JWKS.privateExponent
// )
// try app.jwt.signers.use(jwk: key)


// Environment+App.swift

// JSON Web Keypair Set
extension Environment {
  struct JWKS {
    static let keyID = Environment.get("JWKS_KID")!
    static let modulus = Environment.get("JWKS_N")!
    static let exponent = Environment.get("JWKS_E")!
    static let privateExponent = Environment.get("JWKS_D")!
  }
}
```

### JWT Resources
* Swift 5 Microservices Development book
* https://mkjwk.org/
* [Vapor JWT docs](https://docs.vapor.codes/4.0/jwt/)
* https://tools.ietf.org/html/rfc7517
* https://codebeautify.org/jsonminifier


## Testing

Options for API testing include Postman (REST), Insomnia (GraphQL) and Swell (HTTP/2, websockets, GraphQL), [read more about them](https://medium.com/swlh/electron-apps-for-api-testing-1d23eead8045).

### Postman

Postman can be setup with environments to help manage configuration of APIs. Environment variables can be set off using Test scripts which has access to Postman request and response. However Postman can't use async and await so setting variables off the body is difficult and it would have to use setTimeouts to do it until the support for this is added

#### Resources
* [Postman Learning Center](https://learning.postman.com/)
* [Set Bearer Token as Environment Variable in Postman for All APIs](https://medium.com/@iroshan.du/set-bearer-token-as-environment-variable-in-postman-for-all-apis-13277e3ebd78)
* [How to update environment variables based on a response in Postman](https://ppolyzos.com/2016/11/20/how-to-update-environment-variables-based-on-a-response-in-postman/)


## Fluent

### Resources
* [Vapor Fluent doc](https://docs.vapor.codes/4.0/fluent/overview/#eager-load)
* [Server Side Swift with Vapor 4 Fluent section](https://www.raywenderlich.com/books/server-side-swift-with-vapor/v3.0.ea1/chapters/5-fluent-persisting-models)


## Routing

### Resources
* [Vapor Routing doc](https://docs.vapor.codes/4.0/routing/)


## Logging

### Resources
* [Vapor Logging doc](https://docs.vapor.codes/4.0/logging/)
* [Server Side Swift with Vapor 4 Async section](https://www.raywenderlich.com/books/server-side-swift-with-vapor/v3.0.ea1/chapters/4-async)
* Swift 5 Microservices Development book page 44


## Errors

### Resources
* [Vapor Error doc](https://docs.vapor.codes/4.0/errors/)
* [Swift error handling guide](https://docs.swift.org/swift-book/LanguageGuide/ErrorHandling.html)


## Async

### Resources
* [Vapor Async doc](https://docs.vapor.codes/4.0/async/)


## Client

### Resources
* [Vapor Client doc](https://docs.vapor.codes/4.0/client/)
* [Vapor Content doc](https://docs.vapor.codes/4.0/content/)
* [async-http-client](https://github.com/swift-server/async-http-client)


## Validation

### Resources
* [Vapor Validation docs](https://docs.vapor.codes/4.0/validation/)


## Parameters

###
* [Vapor Parmaters doc](https://docs.vapor.codes/4.0/routing/#parameters)


## Development Setup

This app uses [ngrok](https://ngrok.com/) for local development

Commands:
* `ngrok http -hostname=melo.ngrok.io 8080`

## Bugs

### Xcode processes kill bug

Xcode doesn't terminate process properly. From time to time you will need to manually kill them in terminal
inorder to run application again. If you don't kill and try to run again the application will crash as address in use.
Enter `lsof -1:8080` to list the processes and `kill <PROCESS_ID>` to kill the process.

## gcloudignore

When building with Google Cloud all the files that are ignored in `.gitignore` will be ignored in the build unless `.gitignore` is ignored in `.gcloudignore`. You can also specify to include all the the files from that file in `.gcloudignore` if you want it declarative. Information around how to set up [.gcloudignore](https://cloud.google.com/sdk/gcloud/reference/topic/gcloudignore). See more infomration on gitignore and gcloud ignore [here](https://stackoverflow.com/a/56963506)

## Docker

### Environment variables
Docker environment variables that are JSON need to be wrapped in `''` as described [here](https://stackoverflow.com/questions/53178268/setting-environment-variable-in-a-docker-container)


## Vapor

### Client
Sending requests from Vapor. Read more in the [client docs](https://docs.vapor.codes/4.0/client/)

### Fluent

* [Relations](https://docs.vapor.codes/4.0/fluent/relations/)

## Google Cloud

* [Setting up additional databases](https://cloud.google.com/sql/docs/postgres/create-manage-databases)

### Files

* [Loading resources](https://adrian.schoenig.me/blog/2020/04/11/vapor-loading-resources/) - 11 April 2020