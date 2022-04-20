# 03. Helm Create

The `helm create` command creates a scaffold of a chart.

```sh
$ helm create kubeacademy
Creating kubeacademy
$ tree kubeacademy
kubeacademy
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

3 directories, 10 files
```

## Chart.yaml

The `Chart.yaml` file contains the metadata for the chart:

```yaml
apiVersion: v2
name: kubeacademy
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.16.0"
```

- `name` shows the name of the chart.
- `description` is a description of what the chart provides.
- `type` shows the type of chart (can be `application` or `library`)
- `version` is the version of the chart
- `appVersion` is the version of the application we are packaging

## values.yaml

The `values.yaml` file contains the default value for each parameter a chart accepts:

## charts/

The `charts` directory contains all the charts that our chart depends on.

## templates

The most important part of the chart.

This is where the YAML definitions of our Kubernetes objects live.

### NOTES.txt

This is the information we see when we interact with the chart.

It should provide useful information about the chart (how to access the application, credentials, etc.).

Let's replace it with something generic:

```sh
echo "This is my first chart." > NOTES.txt
```

Now we can install the chart:

```sh
$ helm install jenkins kubeacademy/
NAME: jenkins
LAST DEPLOYED: Sun Mar 20 13:42:53 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
This is my first chart.
```

We see the new contents of `NOTES.txt` printed on the console.
