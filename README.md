# Mastodon Helm Chart

## Introduction

This [Helm](https://helm.sh/) chart facilitates the installation of Mastodon on a Kubernetes cluster. It has been tested with Kubernetes 1.21+ and Helm 3.8.0+.

## Quick Start

1. Modify `values.yaml` or create a separate YAML file for custom values.
2. Run `helm dep install`.
3. Install the chart:
   ```
   helm install --namespace mastodon --create-namespace my-mastodon ./ -f path/to/additional/values.yaml
   ```

## Important Notice: Future Deprecation

We are planning to deprecate this chart in favor of a [new GitHub repository](https://github.com/mastodon/helm-charts). The new repository will offer proper Helm repository support (e.g., `helm repo add`) and will contain multiple charts for Mastodon and its supplementary components.

While we continue to welcome suggestions and PRs to improve this chart, we will not be approving large PRs or changes to fundamental chart functions. These changes should be directed to the new charts.

For more information and discussion, please refer to the pinned [GitHub issue](https://github.com/mastodon/chart/issues/129).

## Configuration

### Required Configuration

- Passwords and keys in the `mastodon.secrets`, `postgresql`, and `redis` groups. If left blank, some values will be auto-generated but won't persist across upgrades.
- SMTP settings for your mailer in the `mastodon.smtp` group.

### Pod Affinity Configuration

If your PersistentVolumeClaim is `ReadWriteOnce` and you're unable to use an S3-compatible service or run a self-hosted compatible service like [Minio](https://min.io/docs/minio/kubernetes/upstream/index.html), you need to set pod affinity to ensure web and sidekiq pods are scheduled on the same node.

Example configuration:

```yaml
podAffinity:
  requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
          - key: app.kubernetes.io/part-of
            operator: In
            values:
              - rails
      topologyKey: kubernetes.io/hostname
```

## Administration

You can run [admin CLI](https://docs.joinmastodon.org/admin/tootctl/) commands in the web deployment:

```bash
kubectl -n mastodon exec -it deployment/mastodon-web -- bash
tootctl accounts modify admin --reset-password
```

or

```bash
kubectl -n mastodon exec -it deployment/mastodon-web -- tootctl accounts modify admin --reset-password
```

## Current Limitations

This chart does not currently support:
- Hidden services
- Swift

## Upgrading

Due to the separation of database migrations as a Job from the Rails and Sidekiq deployments, they may occasionally occur in the wrong order. After upgrading Mastodon versions, it might be necessary to manually delete the Rails and Sidekiq pods so they are recreated with the latest migration.

## Support

For questions, issues, or contributions, please open an issue or pull request in our GitHub repository.
