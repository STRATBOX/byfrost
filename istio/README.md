# Istio Installation via Helm

## Install Helm Chart

```sh
# add istio charts to repo
$ helm repo add istio https://storage.googleapis.com/istio-release/releases/1.4.5/charts/
```

helm repo update

```sh
# update the helm repp
$ helm repo update

# verify istio added
$ helm repo list | grep istio
```

output should look like below
```
istio	https://storage.googleapis.com/istio-release/releases/1.4.5/charts/
    
```

Install Istio’s Custom Resource Definitions (CRD) with the helm chart. This command also creates a Pod namespace called istio-system which you will continue to use for the remainder of this guide.

```sh
$ helm install istio-init istio/istio-init
NAME: istio-init
LAST DEPLOYED: Fri Feb 21 22:28:47 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Verify that all CRDs were successfully installed:

```sh
$ kubectl get crds | grep 'istio.io' | wc -l
23
```
If the number is less, you may need to wait a few moments for the resources to finish being created.

Install the Helm chart for Istio. There are [many installation options available](https://istio.io/docs/reference/config/installation-options/) for Istio. For this guide, the command enables Grafana, which you will use later to visualize your cluster’s data.

```sh
$ helm install istio istio/istio --set grafana.enabled=true
NAME: istio
LAST DEPLOYED: Fri Feb 21 22:36:34 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing Istio.

Your release is named Istio.

To get started running application with Istio, execute the following steps:
1. Label namespace that application object will be deployed to by the following command (take default namespace as an example)

$ kubectl label namespace default istio-injection=enabled
$ kubectl get namespace -L istio-injection

2. Deploy your applications

$ kubectl apply -f <your-application>.yaml

For more information on running Istio, visit:
https://istio.io/
```

Verify that the Istio services and Grafana are running:
```
$ kubectl get svc
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                                                                                      AGE
grafana                  ClusterIP      10.106.76.49     <none>        3000/TCP                                                                                                                                     82s
istio-citadel            ClusterIP      10.96.215.63     <none>        8060/TCP,15014/TCP                                                                                                                           82s
istio-galley             ClusterIP      10.105.110.16    <none>        443/TCP,15014/TCP,9901/TCP                                                                                                                   82s
istio-ingressgateway     LoadBalancer   10.104.150.137   <pending>     15020:32007/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30498/TCP,15030:32258/TCP,15031:30741/TCP,15032:32083/TCP,15443:30904/TCP   82s
istio-pilot              ClusterIP      10.100.224.185   <none>        15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       82s
istio-policy             ClusterIP      10.96.21.238     <none>        9091/TCP,15004/TCP,15014/TCP                                                                                                                 82s
istio-sidecar-injector   ClusterIP      10.106.75.75     <none>        443/TCP,15014/TCP                                                                                                                            82s
istio-telemetry          ClusterIP      10.96.243.7      <none>        9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       82s
kubernetes               ClusterIP      10.96.0.1        <none>        443/TCP                                                                                                                                      10m
prometheus               ClusterIP      10.106.4.249     <none>        9090/TCP                                                                                                                                     82s
```

You can also see the Pods that are running by using this command:

```sh
$ kubectl get pods
NAME                                      READY   STATUS      RESTARTS   AGE
grafana-6c9f84db96-cbs6m                  1/1     Running     0          2m39s
istio-citadel-767fbd8d-mlblg              1/1     Running     0          2m39s
istio-galley-58f8b879b8-cb4rd             1/1     Running     0          2m39s
istio-ingressgateway-76c54cc884-z6zzs     0/1     Running     0          2m39s
istio-init-crd-10-1.4.5-f9hk6             0/1     Completed   0          10m
istio-init-crd-11-1.4.5-dp22p             0/1     Completed   0          10m
istio-init-crd-14-1.4.5-68zf6             0/1     Completed   0          10m
istio-pilot-864db8856-jdqnw               0/2     Pending     0          2m39s
istio-policy-58bcd7f489-sfgjb             0/2     Pending     0          2m39s
istio-sidecar-injector-7b4d98cf78-bhvsr   1/1     Running     0          2m39s
istio-telemetry-75b5bf798d-mmd6q          2/2     Running     3          2m39s
prometheus-794594dc97-h8tm9               1/1     Running     0          2m39s
```

Before moving on, be sure that all Pods are in the `Running` or `Completed` status.

> Note
> If you need to troubleshoot, you can check a specific Pod by using kubectl, remembering that you set the namespace to istio-system:
> `kubectl describe pods pod_name -n pod_namespace`
> And check the logs by using:
> `kubectl logs pod_name -n pod_namespace`

### Set envoy proxies

Istio’s service mesh runs by employing sidecar proxies. You will enable them by injecting them into the containers. This command is using the default namespace which is where you will be deploying the Bookinfo application.

```sh
$ kubectl label namespace default istio-injection=enabled
```

Verify that the ISTIO-INJECTION was enabled for the default namespace:

```
$ kubectl get namespace -L istio-injection
```

### Links
- [How to deploy Istio with Kubernetes | Linode](https://www.linode.com/docs/kubernetes/how-to-deploy-istio-with-kubernetes/)
