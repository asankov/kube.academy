# 02. Helm Tool

Download binaries from <https://helm.sh/docs/intro/install/>.

## Repositories

Helm charts are located in repositories.

Some public and free Helm repositories are:

- <https://artifacthub.io/> by CNCF
- <https://hub.kubeapps.com/> by VMware

## Installing a chart

To install a chart you need to add the chart repository locally:

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
```

update the repo to get the latest data:

```sh
helm repo update
```

and then install the desired chart:

```sh
helm install my-release bitnami/drupal
```

In this command `bitnami/drupal` is the name of the chart.
It follows convention similar to how container images are named.

`my-release` is the name of the Helm release we are installing.
A release is one installation of a chart.
We can deploy one chart multiple times in different releases without them colliding.

### Customizing a release

Each Chart exposes values that can be customized when deploying the chart.

Some of the values may be required, while others can be optional with default values.

When deploying a Helm chart we can customize these values either via the `--set` flag of the `helm` tool or via providing a `values.yaml` file that contains all the values.

For example, for the Drupal chart we can customize the username and password of the admin user my setting the following values:

```sh
helm install my-release --set drupalUsername=kubeacademy --set drupalPassword=password123 bitnami/drupal
```

or via a YAML file:

```sh
helm install my-release -f values.yaml bitnami/drupal
# OR
helm install my-release --values values.yaml bitnami/drupal
```

where `values.yaml` is:

```yaml
drupalUsername: kubeacademy
drupalPassword: password123
```

## Searching a repo

Once we have added a repository to the `helm` tool we can use it to search that repository:

```sh
helm search repo bitnami
```

shows all the charts available in the `bitnami` repo.

```sh
helm search repo bitnami/rabbitmq
```

shows all available versions of the `rabbitmq` chart in the `bitnami` repo.

```sh
$ helm search repo bitnami/rabbitmq -l
NAME                                CHART VERSION   APP VERSION DESCRIPTION
bitnami/rabbitmq                    8.30.0          3.9.13      RabbitMQ is an open source general-purpose mess...
...
```

Each chart has 2 versions - `CHART VERSION` and `APP VERSION`.

The app version is the version of the app itself.

The chart version is the version of the chart.

We can have the same version of the app, but multiple versions of the chart.
This means that the app did not change, but something in the chart did (for example, we have more options for customization).

## Installing a specific version

To install a specific version of a chart we can use the `--version` flag:

```sh
helm install myrabbitmq bitnami/rabbitmq --version 6.18.3
```

To see the Readme of a specific chart version we can use the following command:

```sh
helm show readme bitnami/rabbitmq --version 6.18.3
```

## Upgrading a chart

To upgrade a chart with new parameters use the `helm upgrade` command:

```sh
helm upgrade myrabbitmq bitnami/rabbitmq --version 6.18.3 --set replicas=3
```

We can also upgrade to a new version of the chart(or the app):

```sh
helm upgrade myrabbitmq bitnami/rabbitmq --version 6.27.0 --set replicas=3
```

## Seeing the history of a release

We can check the history of a Helm release via the `helm history` command:

```sh
helm history myrabbitmq
```

## Rolling back a release

We can rollback a release via the `helm rollback` command:

```sh
helm rollback myrabbitmq
```

## Uninstalling a release

We can uninstall a release via the `helm uninstall` command:

```sh
helm uninstall myrabbitmq
```
