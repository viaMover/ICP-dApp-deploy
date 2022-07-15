# IC dApp deployment
Every dApp (decentralized application) should always be looking to expand hosting resilience capabilities. Besides centralised hosting solutions, there are multiple decentralized: like IPFS and Internet Canitsters by [Internet Computer](https://internetcomputer.org).

This guide is aimed to cover the deployment process on ICs for any dApp, as well as solutions to potentials errors and hurdles.

By the way, Mover is now available on ICP, try it yourself:

[Website](https://ttsp4-caaaa-aaaad-qdbfa-cai.ic.fleek.co): [ttsp4-caaaa-aaaad-qdbfa-cai](https://k7gat-daaaa-aaaae-qaahq-cai.ic0.app/canister/ttsp4-caaaa-aaaad-qdbfa-cai)

[Web app](https://v7277-viaaa-aaaad-qdbsq-cai.ic.fleek.co/): [v7277-viaaa-aaaad-qdbsq-cai](https://k7gat-daaaa-aaaae-qaahq-cai.ic0.app/canister/v7277-viaaa-aaaad-qdbsq-cai)

## How to deploy on Internet Canister?

### Docker image
The first thing you need to do is to make a list of the basic dependencies that are needed to build your application. For example, our `webApp` [application](https://github.com/viaMover/web-app) requires Nodejs version 14.18 and the yarn package manager, respectively, we will use `node: 14.18` image, in which yarn is already installed.

However, the production build requires to decrypt the secrets, so we use the alpine-based image: `node:14.18-alpine3.15`

### Build command

In most cases, it is enough to use two commands with the combined operator `&&` (This operator will prevent further execution of commands if at least one ends with an error), as the instruction prescribes:

    yarn && yarn build

or for npm

    npm install && npm run build

However, in this case, yarn (or npm) can update their lock files, to prevent this we use:

    yarn install --frozen-lockfile && yarn build

or for npm:

    npm ci && npm run build

If the application requires additional dependencies to build, for example, "python2" or "gcc", you need to install them using the && operator

    apk update && apk add gcc make python2 && npm ci && npm run build

After a successful build, the application will be automatically uploaded to ICP

## Possible errors
Our web app contains a lot of assets, has deep web3 components dependencies, and is not a static app. That is why during the deployment we came across a few hurdles. We wanted to share what we've learned with other developers, as that will save precious time.


**Problem:** Application deployment to ICP ends with an error

> The Replica returned an error: code 5, message: "Canister <ID> exceeded the instruction limit for
> single message execution.

**Fix:** Your application either has too many files, or the weight of each is too large (since large files are loaded using file chunking, it may happen that your total files, for example, are 50, but with chunking there are 10 times more).  The solution in this situation is to reduce the total number of files, or reduce the weight of each, to prevent chunking. For example, you can move all assets to S3 storage.


**Problem:** Application deployment to ICP ends with an error

> The replica returned an HTTP Error: Http Error: status 404 Not Found, content type "application/cbor", content: Canister <ID> not found

or
> The replica returned an HTTP Error: Http Error: status 504 Gateway Timeout, content type "text/html", content: ...

or

> The replica returned an HTTP Error: status 502 Bad Gateway, content type "text/html" Error occurred during the upload

**Fix:** In this case the best solution is to wait and try again later, as the error is on the server side.
