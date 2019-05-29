<p align="center">
  <a href="https://keel.sh" target="_blank"><img width="100"src="https://keel.sh/images/logo.png"></a>
</p>

<p align="center">
   
  <a href="https://hub.docker.com/r/keelhq/keel/">
    <img src="https://circleci.com/gh/keel-hq/keel/tree/master.svg?style=shield&circle-token=0239846a42cfa188de531058b9a2116a4b8600d8" alt="CircleCI">
  </a>
  
  <a href="https://goreportcard.com/report/github.com/keel-hq/keel">
    <img src="https://goreportcard.com/badge/github.com/keel-hq/keel" alt="Go Report">
  </a>
  
  <a href="https://img.shields.io/docker/pulls/keelhq/keel.svg">
    <img src="https://img.shields.io/docker/pulls/keelhq/keel.svg" alt="Docker Pulls">
  </a>

  <a href="https://drone-kr.webrelay.io/keel-hq/keel">
    <img src="https://drone-kr.webrelay.io/api/badges/keel-hq/keel/status.svg" alt="Drone Status">
  </a>
</p>

# Keel - automated Kubernetes deployments for the rest of us

* Website [https://keel.sh](https://keel.sh)
* Slack - [kubernetes.slack.com](https://kubernetes.slack.com) look for channel #keel

Keel is a tool for automating [Kubernetes](https://kubernetes.io/) deployment updates. Keel is stateless, robust and lightweight.

Keel provides several key features:

* __[Kubernetes](https://kubernetes.io/) and [Helm](https://helm.sh) providers__ - Keel has direct integrations with Kubernetes and Helm.

* __No CLI/API__ - tired of `f***ctl` for everything? Keel doesn't have one. Gets job done through labels, annotations, charts.

* __Semver policies__ - specify update policy for each deployment/Helm release individually.

* __Automatic [Google Container Registry](https://cloud.google.com/container-registry/) configuration__ - Keel automatically sets up topic and subscriptions for your deployment images by periodically scanning your environment.

* __[Native, DockerHub, Quay and Azure container registry webhooks](https://keel.sh/v1/guide/documentation.html#Triggers) support__ -  once webhook is received impacted deployments will be identified and updated.

*  __[Polling](https://keel.sh/v1/guide/documentation.html#Polling)__ - when webhooks and pubsub aren't available - Keel can still be useful by checking Docker Registry for new tags (if current tag is semver) or same tag SHA digest change (ie: `latest`).

* __Notifications__ - out of the box Keel has Slack, Hipchat, Mattermost and standard webhook notifications, more info [here](https://keel.sh/v1/guide/documentation.html#Notifications)

<p align="center">
  <a href="https://keel.sh" target="_blank"><img width="700"src="https://keel.sh/images/keel-overview.png"></a>
</p>

### Support

Support Keel's development by:
* [Patreon](https://patreon.com/keel)
* [Paypal](https://www.paypal.me/keelhq)
* Star this repository
* [Follow on Twitter](https://twitter.com/keel_hq)

### Warp speed quick start

To achieve warp speed, we will be using [sunstone.dev](https://about.sunstone.dev) service and Minikube. 

Start Minikube:


```bash
minikube start
```

Install customized Keel (feel free to change credentials, namespace and version tag) straight from your `kubectl`.

```bash
# To override default latest semver tag, add &tag=x.x.x query argument to the URL below
kubectl apply -f https://sunstone.dev/raw.githubusercontent.com/keel-hq/keel/feature/ui/deployment/deployment-template.yaml?namespace=default&username=admin&password=admin
# and get Keel IP:
minikube service --namespace default keel --url
http://192.168.99.100:3199
```

Open it in your browser. UI will let you


### Helm quick start

Prerequisites:

* [Helm](https://docs.helm.sh/using_helm/#installing-helm)
* Kubernetes

You need to add this Chart repo to Helm:

```bash
helm repo add keel https://charts.keel.sh 
helm repo update
```

Install through Helm (with Helm provider enabled by default):

```bash
helm upgrade --install keel --namespace=kube-system keel/keel
```

If you work mostly with regular Kubernetes manifests, you can install Keel without Helm provider support:

```bash
helm upgrade --install keel --namespace=keel keel/keel --set helmProvider.enabled="false" 
```

That's it, see [Configuration](https://github.com/keel-hq/keel#configuration) section now.

### Quick Start

<p align="center">
  <a href="https://keel.sh" target="_blank"><img width="700"src="https://keel.sh/images/keel-workflow.png"></a>
</p>

A step-by-step guide to install Keel on your Kubernetes cluster is viewable on the Keel website:

[https://keel.sh/v1/guide/quick-start.html](https://keel.sh/v1/guide/quick-start.html)

### Configuration

Once Keel is deployed, you only need to specify update policy on your deployment file or Helm chart:

<p align="center">
  <a href="https://keel.sh/v1/guide/" target="_blank"><img width="700"src="https://keel.sh/images/keel-minimal-configuration.png"></a>
</p>

No additional configuration is required. Enabling continuous delivery for your workloads has never been this easy!

### Documentation

Documentation is viewable on the Keel Website:

[https://keel.sh/v1/guide/documentation](https://keel.sh/v1/guide/documentation)


### Contributing

Before starting to work on some big or medium features - raise an issue [here](https://github.com/keel-hq/keel/issues) so we can coordinate our efforts.

### Developing Keel

If you wish to work on Keel itself, you will need Go 1.12+ installed. Make sure you put Keel into correct Gopath and `go build` (dependency management is done through [dep](https://github.com/golang/dep)). 

To test Keel while developing:

1. Launch a Kubernetes cluster like Minikube or Docker for Mac with Kubernetes.
2. Change config to use it: `kubectl config use-context docker-for-desktop`
3. Build Keel from `cmd/keel` directory. 
4. Start Keel with: `keel --no-incluster`. This will use Kubeconfig from your home. 

### Running unit tests

Get a test parser (makes output nice):

```bash
go get github.com/mfridman/tparse
```

To run unit tests:

```bash
make test
```

### Running e2e tests

Prerequisites:
- configured kubectl + kubeconfig
- a running cluster (test suite will create testing namespaces and delete them after tests)
- Go environment (will compile Keel before running)

Once prerequisites are ready:

```
make e2e
```