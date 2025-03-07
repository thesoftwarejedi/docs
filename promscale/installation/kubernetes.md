---
title: Kubernetes
excerpt: Install Promscale on a Kubernetes cluster
product: promscale
keywords: [Kubernetes, analytics, Helm]
tags: [install]
related_pages:
  - /promscale/:currentVersion:/guides/resource-recomm/
  - /promscale/:currentVersion:/send-data/
---

import PromscaleInstallPrerequisite from "versionContent/_partials/_promscale-install-pre-requisite.mdx";
import PromscaleSendData from "versionContent/_partials/_promscale-send-data.mdx";
import PromscaleDeprecation from "versionContent/_partials/_deprecated-promscale.mdx";

# Install Promscale on Kubernetes

<PromscaleDeprecation />

You can install Promscale on Kubernetes using Helm or using a manifest file.

<PromscaleInstallPrerequisite />

## Install Promscale with Helm

You can install Promscale using Helm charts.

Before you begin, you must have installed Helm. For more information, including
packages and installation instructions, see the
[Helm documentation][install-helm].

The Helm charts must be installed
in this order:

1.  Install the TimescaleDB Helm chart
1.  Install the Promscale Helm chart

### Install the TimescaleDB Helm chart

Before you install the TimescaleDB Helm chart, you need to configure these
settings in the [`values.yaml`][timescaledb-single-values-yaml] configuration file:

*   [Credentials for the superuser, admin, and other users][timescaledb-helm-values-creds]
*   [TLS Certificates][timescaledb-helm-values-certs]
*   **Optional:** `pgbackrest` [configuration][timescale-backups]

<Highlight type="note">
If you do not configure the user credentials before you start, they are randomly
generated. When this happens, the `helm upgrade` command does not rotate the
credentials, to prevent breaking the database by changing the database
credentials instead it uses the same credentials that are generated during the
`helm install`.
</Highlight>

By default, the `timescaledb-single` Helm chart deploys TimescaleDB in
high availability mode. This creates three database replicas,
which consumes three times the amount of disk space. Each database
instance mounts to its own persistent volume claim (PVC).

You can turn off high availability mode by changing the value of `replicaCount`
to `1` in
[`values.yaml`][timescaledb-single-values-yaml].

<Procedure>

#### Disabling TimescaleDB high availability mode

1.  Download the default [`values.yaml`][timescaledb-single-values-yaml] file for the `timescaledb-single` Helm chart.
1.  In `values.yaml`, change the default `replicaCount` from `3` to `1`.
1.  Use this `values.yaml` file with the `-f` flag when installing the `timescaledb-single` Helm chart.
    For installation instructions, see [the procedures for installing the Helm chart](#installing-the-timescaledb-helm-chart).

</Procedure>

<Procedure>

#### Installing the TimescaleDB Helm chart

1.  Add the TimescaleDB Helm chart repository:

    ```bash
    helm repo add timescale 'https://charts.timescale.com'
    ```

1.  Check that the repository is up to date:

    ```bash
    helm repo update
    ```

1.  Install the TimescaleDB Helm chart, using a release name of your choice:

    ```bash
    helm install <RELEASE_NAME> timescale/timescaledb-single
    ```

</Procedure>

You can provide arguments to the `helm install` command using this format:
`--set key=value[,key=value]`. For example, to install the  chart with backups
enabled, use this command:

```bash
helm install <RELEASE_NAME> timescale/timescaledb-single --set backup.enabled=true
```

Alternatively, you can provide a YAML file that includes parameters for
installing the chart, like this:

```bash
helm install <RELEASE_NAME> -f myvalues.yaml timescale/timescaledb-single
```

### Install the Promscale Helm chart

When you have your TimescaleDB Helm chart installed, you can install the
Promscale Helm chart. Promscale needs to access your TimescaleDB database. You
can provide the database URI, or specify connection parameters.

<Procedure>

#### Installing the Promscale Helm chart

1.  Add the TimescaleDB Helm chart repository:

    ```bash
    helm repo add timescale 'https://charts.timescale.com'
    ```

1.  Check that the repository is up to date:

    ```bash
    helm repo update
    ```

1.  Create a database called `tsdb` for Promscale data:

    ```bash
    kubectl exec -i --tty $(kubectl get pod -o name --namespace <NAMESPACE> -l role=master,release=<RELEASE_NAME>) -- psql -U postgres
    CREATE DATABASE tsdb WITH OWNER postgres;
    \q
    ```

1.  Capture the `postgres` user password:

    ```bash
    echo $(kubectl get secret --namespace default <RELEASE_NAME>-credentials -o jsonpath="{.data.PATRONI_SUPERUSER_PASSWORD}" | base64 --decode)
    ```

1.  Download the Promscale
    [values.yaml][promscale-values-yaml], and update the `connection` section
    with your TimescaleDB connection details.
    Add or edit this section with your TimescaleDB connection details:
    <Terminal>

    <tab label='Database URI'>

    ```yaml
    connection:
      uri: postgres://<username>:<password>@<host>:<port>/<dbname>?sslmode=require
    ```

    </tab>

    OR

    <tab label="Connection parameters">

    ```yaml
    connection:
      user: postgres
      password: ""
      host: <RELEASE_NAME>.<NAMESPACE>.svc.cluster.local
      port: 5432
      sslMode: require
      dbName: tsdb
    ```

    </tab>

    </Terminal>

1.  Install the Promscale Helm chart:

    ```bash
    helm install <RELEASE_NAME> timescale/promscale -f values.yaml
    ```

    <Highlight type="note">
    Replace `&lt;RELEASE_NAME&gt;` with the name of your choice
    </Highlight>

</Procedure>

<PromscaleSendData />

[install-binary]: /promscale/:currentVersion:/installation/binary/
[install-helm]: /promscale/:currentVersion:/installation/kubernetes/#install-promscale-with-helm
[promscale-values-yaml]: https://github.com/timescale/helm-charts/blob/main/charts/promscale/values.yaml
[send-data]: /promscale/:currentVersion:/send-data/
[template-manifest]: https://github.com/timescale/promscale/blob/0.13.0/deploy/static/deploy.yaml
[timescale-backups]: https://github.com/timescale/timescaledb-kubernetes/tree/master/charts/timescaledb-single#create-backups-to-s3
[timescaledb-helm-values-certs]: https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-single/values.yaml#L45
[timescaledb-helm-values-creds]: https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-single/values.yaml#L33
[timescaledb-single-values-yaml]: https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-single/values.yaml
