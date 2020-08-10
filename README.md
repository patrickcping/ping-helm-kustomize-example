# Ping with Helm - Waiting for dependent services

With Helm you can deploy Ping deployable products to kubernetes with a single command.

When deploying multiple services however, you may find the need to stall the start process of some products until dependent products have been started.

In this example, I will show how to deploy PingFederate and PingDirectory clusters together, where PingFederate waits until the directory is available.

The example uses Kustomize as a post-processor to Helm (version 3.1 onwards) and assumes a Kubernetes cluster is available and set in kubeconfig context.

1. Clone the repository
```shell
git clone https://github.com/patrickcping/ping-helm-kustomize-example.git
```

2. If not already, add the Sample Ping Helm charts repository as described [here](https://github.com/patrickcping/pingidentity-helm-charts/blob/master/README.md)

3. Run the Helm install command for PingFederate as a cluster.  Available options are described [here](https://github.com/patrickcping/pingidentity-helm-charts/blob/master/pingfederate/README.md)
```shell
helm install \
pingfederate-cluster --debug \
--set pingfederate.clustering.enabled=true \
--set license.useDevOpsKey=true \
--set license.devOpsKey.user=${PING_IDENTITY_DEVOPS_USER} \
--set license.devOpsKey.key=${PING_IDENTITY_DEVOPS_KEY} \
--set license.acceptEULA=yes \
--post-renderer ./kustomize \
pingidentity-pc/pingfederate
```

Notice that the admin pod will have a status of `Init:0/1`

4. Run the Helm install command for PingDirectory.  Available options are described [here](https://github.com/patrickcping/pingidentity-helm-charts/blob/master/pingdirectory/README.md)
```shell
helm install \
pingdirectory --debug \
--set license.useDevOpsKey=true \
--set license.devOpsKey.user=${PING_IDENTITY_DEVOPS_USER} \
--set license.devOpsKey.key=${PING_IDENTITY_DEVOPS_KEY} \
--set license.acceptEULA=yes \
--set persistentvolume.enabled=false \
pingidentity-pc/pingdirectory
```

Once PingDirectory has started, and is ready, PingFederate will continue with the startup process