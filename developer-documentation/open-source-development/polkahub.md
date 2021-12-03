# Polkahub

### Introduction <a href="#introduction" id="introduction"></a>

PolkaHub aims to create a managed container system, with one-click services for deploying and running different parachain nodes. The proposed system enables automatic updates and resource management for running nodes, additionally providing templates for launching parachains.

### Github Repo:

{% embed url="https://github.com/akropolisio/polkahub-monorepo" %}

### What is PolkaHub? <a href="#what-is-polkahub" id="what-is-polkahub"></a>

1. Polkahub is a fast, scalable blockchain infrastructure component for Substrate parachains.
2. Polkahub provides parachain developers with the ability to launch and manage network infrastructure using our command line utility and the ability to provide public node access.
3. Polkahub provides developers with a unified standard for packaging and deploying applications to cloud infrastructure.
4. Polkahub provides the functionality to track and control the parachain’s versions. So in the event of critical bugs arising, developers can update or roll back the version of the parachain using simple commands (via running specific commands in a command line).
5. Polkahub supports Substrate node deployment to remote servers or cloud infrastructure via git.
6. Polkahub infrastructure is based on Docker Container Services such as Kubernetes.
7. Docker provides high-level interfaces for isolated environments within the node's execution. Easily scaled, managed and updated.
8. No need to rely on DevOps and System Administration for managing parachain’s infrastructure - thanks to PolkaHub. You only need git, command line and a simple web-interface.

![Polkahub](https://wiki.akropolis.io/images/development/polkahub/polkahub-main.png)

### Quick Start <a href="#quick-start" id="quick-start"></a>

### Install CLI <a href="#install-cli" id="install-cli"></a>

MacOS / Linux You can just use bash script:

```
$ bash <(curl http://get.polkahub.org/ -L)
```

This will install polkahub binary in your /usr/local/bin(MacOS) or /usr/bin(Linux) directory Then you can use it:

```
$ polkahub <action> [ARGS]
```

Or use docker image like this:

```
$ mkdir $HOME/.polkahub
$ docker run --rm -ti -v $HOME/.polkahub_docker:/tmp/home -e POLKAHUB_HOME=/tmp/home registry.polkahub.org/polkahub-cli:v3 <action> [ARGS]
```

### Usage CLI <a href="#usage-cli" id="usage-cli"></a>

#### Authentication <a href="#authentication" id="authentication"></a>

```
$ polkahub auth
Email: user@example.com
Password:

Login user with email user@example.com
 ⠂⠂⠂
done
```

Use email and password created via https://polkahub.org or create new email and password via CLI (see Registration section)

#### Registration <a href="#registration" id="registration"></a>

```
$ polkahub register
Email: user@example.com
Password:
Confirm Password:

Registration new user with email user@example.com
 ⡀⡀⡀
done
```

#### Create a new project <a href="#create-a-new-project" id="create-a-new-project"></a>

```
$ polkahub create akropolisos

 ⠁⠁⠁
done
https     -> "https://steadfast-surprise-6647-akropolisos-rpc.polkahub.tech"
ws        -> "wss://steadfast-surprise-6647-akropolisos.polkahub.tech"
remote  -> "https://git.polkahub.org/steadfast-surprise-6647-akropolisos.git"
```

Then you can add **remote** to your project, push it and it will automatically start CI build.

#### Find a project <a href="#find-a-project" id="find-a-project"></a>

```
$ polkahub find akropolisos

Looking for akropolisos project
 ⠁⠁⠁
steadfast-surprise-6647/akropolisos@0.8.2
steadfast-surprise-6647/akropolisos@v1
steadfast-surprise-6647/akropolisos@v2
```

#### Install a exists project <a href="#install-a-exists-project" id="install-a-exists-project"></a>

```
$ polkahub install steadfast-surprise-6647/akropolisos@0.8.2 -a alexander 

Deploying akropolisos project with version 0.8.2
 ⠁⠁⠁
done
https  -> "https://frightened-brick-8071-alexander-rpc.polkahub.tech"
ws     -> "wss://frightened-brick-8071-alexander.polkahub.tech"
```

### Deploying private Polkahub <a href="#deploying-private-polkahub" id="deploying-private-polkahub"></a>

### Preliminary steps <a href="#preliminary-steps" id="preliminary-steps"></a>

1. Install Rust on host, where Polkahub components will be build. Manual documentation: https://www.rust-lang.org/learn/get-started
2. Buy a VPS, minimal requirements are 2CPU and 8GB RAM. The more CPU - the faster deployment of projects in Cl
3. Install the Docker and the Docker Compose on host, where Polkahub components will run. Manual documentation: https://docs.docker.com/
4. Install the Docker Registry. You can use public or private one from the cloud provider of your choice. Also you can set up your own one. Manual documentation: https://docs.docker.com/
5. Install and configure the Kubernetes cluster. You can buy a cluster from the cloud provider of your choice or deploy your own one on your server. Manual documentation: https://kubernetes.io/docs/home/
6. Sign up on https://www.cloudflare.com/ and get the token, key\_auth, zone\_name and zone\_id to create the DNS records via https://api.cloudflare.com/
7. Sign up on https://www.mailgun.com/ and get the api\_key and domain\_name to sending emails via https://api.mailgun.net/
8. Install packages for Debian-like Linux distributives: apt-get install -y libssl-dev ca-certificates git curl

#### Building the components <a href="#building-the-components" id="building-the-components"></a>

1. CLI. Building manual:https://github.com/akropolisio/polkahub-cli . To change URL REST API you need to edit constants INSTALL\_URL, FIND\_URL, POLKAHUB\_URL in /src/parsing.rs
2. REST API. Building manual: https://github.com/akropolisio/polkahub-backend
3. Deployer. Building manual: https://github.com/akropolisio/polkahub-deployer
4. Cloudflare Manager. Building manual: https://github.com/akropolisio/cloudflare-manager
5. Mailgun Sender. Building manual: https://github.com/akropolisio/mailgun-sender

#### Running the components in the Kubernetes <a href="#running-the-components-in-the-kubernetes" id="running-the-components-in-the-kubernetes"></a>

1. Pack the Deployer in the Docker-image. Files and environment variables descriptions are here: https://github.com/akropolisio/cloudflare-manager Push to Docker Registry, run the Deployer in Kubernetes cluster and configure the access via HTTP to the Deployer for CI.
2. Pack the Cloud Manager in the Docker-image. Files and environment variables descriptions are here: https://github.com/akropolisio/cloudflare-manager. Secret Files content can be found in point 6 of Preliminary steps section. Push to Docker Registry, run the Cloudflare Manager in Kubernetes cluster and configure the access via HTTP to the Cloudflare Manager for CI.
3. Pack the Mailgun Sender in the Docker-image. Files and environment variables descriptions are here: https://github.com/akropolisio/mailgun-sender. Secret Files content can be found in point 6 of Preliminary steps section. Push to Docker Registry, run the Mailgun Sender in Kubernetes cluster and configure the access via HTTP to the Mailgun Sender for REST API.

#### Running the components on VPS <a href="#running-the-components-on-vps" id="running-the-components-on-vps"></a>

1. Install Jenkins. Manual documentation: https://jenkins.io/download/. Add the access token to REST API, configure 2 jobs: build-new-version and deploy-fixed-version. You may find examples of jobs at jenkins-test.polkahub.org. Configure the access to the Docker server and Docker registry for Jenkins.
2. Deploy the REST API. Environment variables descriptions are here: https://github.com/akropolisio/polkahub-backend Token and job (build-new-version) name can be found in the point 1 of “Running the components on VPS”. Configure the access via HTTP to the REST API for CLI.
3. Deploy the Git server, configure the access via HTTP to the git repositories directories (e.g. via Nginx + fcgiwrap + git-http-backend)
4. Deploy the PostgreSQL, configure the access for REST API

### Project Components <a href="#project-components" id="project-components"></a>

| Name                                                                    | Version | Purpose                                           |
| ----------------------------------------------------------------------- | :-----: | ------------------------------------------------- |
| [cloudflare-manager](https://github.com/akropolisio/cloudflare-manager) |  0.1.0  | DNS management                                    |
| [mailgun-sender](https://github.com/akropolisio/mailgun-sender.git)     |  0.1.0  | Email sender via Mailgun                          |
| [polkahub-backend](https://github.com/akropolisio/polkahub-backend)     |  0.2.0  | Manage user command execution                     |
| [polkahub-cli](https://github.com/akropolisio/polkahub-cli)             |  0.4.0  | Process user input and trigger api calls          |
| [polkahub-deployer](https://github.com/akropolisio/polkahub-deployer)   |  0.2.0  | Form docker manifest for Kubernetes API           |
| [polkahub-installer](https://github.com/akropolisio/polkahub-installer) |  0.4.0  | Shell script to install latest Polkahub CLI build |

### Docker images built <a href="#docker-images-built" id="docker-images-built"></a>

All images available at registry.polkahub.org/`<name>`:`<tag>`

#### General <a href="#general" id="general"></a>

| Name                                                                    |      Tag     | Purpose                                                       |
| ----------------------------------------------------------------------- | :----------: | ------------------------------------------------------------- |
| [deployer](https://github.com/akropolisio/polkahub-deployer)            |      v2      | Microservice to deploy the projects in Kubernetes             |
| [cloudflare-manager](https://github.com/akropolisio/cloudflare-manager) |      v1      | Microservice to create DNS-records in Cloudflare              |
| [mailgun-sender](https://github.com/akropolisio/mailgun-sender.git)     |      v1      | Email sender via Mailgun                                      |
| substrate-builder                                                       | rust-1.41-v3 | Docker-image with the environment for projects building in CI |

#### Pre-Built for test environment <a href="#pre-built-for-test-environment" id="pre-built-for-test-environment"></a>

| Name                                                                |  Tag  | Purpose                                                      |
| ------------------------------------------------------------------- | :---: | ------------------------------------------------------------ |
| [polkahub-backend](https://github.com/akropolisio/polkahub-backend) |   v3  | API image for Polkahub test launch via Docker-compose        |
| polkahub-git-backend                                                |   v1  | Git server image for Polkahub test launch via Docker-compose |
| polkahub-jenkins                                                    |   v1  | Jenkins image for Polkahub test launch via Docker-compose    |
| balancer                                                            |   v3  | Jenkins image for Polkahub test launch via Docker-compose    |
| [polkahub-cli](https://github.com/akropolisio/polkahub-cli)         |   v3  | CLI image interacting with the test environment              |
| akropolis-akropolisos                                               | 0.8.2 | AkropolisOS Node image                                       |
| stimulating-plant-3173-alexander                                    |  v0.4 | Polkadot-Alexander Node Image                                |
| decisive-picture-8329-kusama                                        |  v0.7 | Kusama Node Image                                            |
| polkahub-frontend                                                   |   v1  | Web portal                                                   |
