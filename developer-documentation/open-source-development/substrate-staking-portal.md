# Substrate Staking portal



### Introduction <a href="#introduction" id="introduction"></a>

Existing staking mechanism via [https://polkadot.js.org/apps/](https://polkadot.js.org/apps/) has developer-centric UX and is very complicated for ordinary users. To simplify UX we want to build Staking portal for AkropolisOSChain users.

### Github repo:

{% embed url="https://github.com/akropolisio/staking-portal" %}

### Frontend: <a href="#what-is-polkadot-staking-portal" id="what-is-polkadot-staking-portal"></a>

{% embed url="https://staking-portal.akropolis.io" %}

### What is Polkadot Staking Portal? <a href="#what-is-polkadot-staking-portal" id="what-is-polkadot-staking-portal"></a>

A simple and intuitive interface and Akropolis browser extension will make the staking process accessible to a wide range of users - you only need an account on Polkadot and Polkadot-js for signing transactions.

What you can do with our staking portal:

* Check your overall balance and amount of all bonded tokens - as well as check each wallet connected
* Check the current validators set, their commission, how much is staked for them, etc. and decide whether you want to nominate for them or not.
* Check and edit stake conditions - add/withdraw funds, edit the list of nominees, stop nominating, redeem funds, etc.

### For frontend (in frontend folder) <a href="#for-frontend-in-frontend-folder" id="for-frontend-in-frontend-folder"></a>

#### Install all dependencies <a href="#install-all-dependencies" id="install-all-dependencies"></a>

* `npm i` install frontend and contracts dependencies

#### To start locally <a href="#to-start-locally" id="to-start-locally"></a>

* `npm run dev` for development environment in watch mode
* `npm run prod` for production environment in watch mode

#### To build locally (see build folder) <a href="#to-build-locally-see-build-folder" id="to-build-locally-see-build-folder"></a>

* `npm run build:dev` for development environment without watch mode
* `npm run build:prod` for production environment without watch mode

#### To start bundle analyzer <a href="#to-start-bundle-analyzer" id="to-start-bundle-analyzer"></a>

* `npm run analyze:dev` for development environment
* `npm run analyze:prod` for production environment

#### To start test <a href="#to-start-test" id="to-start-test"></a>

* `npm test` or `npm t` for start test, before that you need start network (`npm run ganache-cli`)

### We used: <a href="#we-use" id="we-use"></a>

* \[x] polkadot.js/api for interacting with Akropolis Chain
* \[x] Typescript
* \[x] React
* \[x] Redux
* \[x] Redux-saga for side-effects
* \[x] Material-UI
