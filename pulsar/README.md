# Pulsar

This directory contains the Helm Chart required to do a complete Pulsar deployment on Kubernetes.

### Prerequisites
1. Create a kubernetes cluster (e.g. with minikube)
```sh
# create kubernetes cluster on minikube
$ minikube start --memory=8192 --cpus=4
```
##### [optional] create kubernetes namespace manually
```sh
# create kubernetes namespace
# Create a pod using the data in pod.json.
$ kubectl create -f ./namespace.json
namespace/destroyer created

# Create a pod based on the JSON passed into stdin.
$ cat namespace.json | kubectl create -f -
namespace/destroyer created
```

##### Alternatively set namespace in `*values.yaml` file
```yaml
## Namespace to deploy pulsar
namespace: pulsar
namespaceCreate: yes
```

## Install Pulsar chart

Note that in the below examples I've named the release `destroyer`

note the values-mini.yaml is used for simple deployment omitting the values flag uses default vaules.yaml file for higher spec deployment

```sh
# from inside the platform.labs repo root
# remember to set namespaceCreate: no inside values-mini.yaml 
# if you created namepace manaually above.
$ helm install destroyer --values pulsar/values-mini.yaml ./pulsar
NAME: destroyer
LAST DEPLOYED: Thu Feb  6 19:04:31 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

check status of the deployment

```sh
$ helm status destroyer
NAME: destroyer
LAST DEPLOYED: Thu Feb  6 19:04:31 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```


See all running kubernetes namespaces - give the cluster some time to spin up all nodes/pods

```sh
$ kubectl get cm --all-namespaces
NAMESPACE              NAME                                 DATA   AGE
default                deathstar-vault-config               1      82m
kube-public            cluster-info                         2      110m
kube-system            coredns                              1      110m
kube-system            extension-apiserver-authentication   6      110m
kube-system            kube-proxy                           2      110m
kube-system            kubeadm-config                       2      110m
kube-system            kubelet-config-1.17                  1      110m
kubernetes-dashboard   kubernetes-dashboard-settings        0      96m
pulsar                 destroyer-pulsar-autorecovery        2      8m28s
pulsar                 destroyer-pulsar-bastion             1      8m28s
pulsar                 destroyer-pulsar-bookkeeper          9      8m28s
pulsar                 destroyer-pulsar-broker              12     8m28s
pulsar                 destroyer-pulsar-prometheus          1      8m28s
pulsar                 destroyer-pulsar-proxy               4      8m28s
pulsar                 destroyer-pulsar-zookeeper           2      8m28s
```

see list of helm charts installed
```sh
$ helm list
NAME     	NAMESPACE	REVISION	UPDATED                             	STATUS  	CHART       	APP VERSION
deathstar	default  	1       	2020-02-06 17:36:38.171866 +0000 UTC	deployed	vault-0.3.3
destroyer	default  	1       	2020-02-06 18:50:27.999343 +0000 UTC	deployed	pulsar-1.0.0	1.0
```

Once the helm chart is completed on installation, you can access the cluster via:

To execute commands in pulsar - need to access the shell of the bastion pod.
```sh
# replace with shell of choice at end of the command
$ kubectl -n pulsar exec $(kubectl get pods --namespace pulsar -l "app=pulsar,component=bastion" -o jsonpath="{.items[0].metadata.name}") -it -- zsh
```
Or can setup aliases of commonly used commands so shell access isn't required each time
```sh
# assumes namespace is pulsar demo
$ alias pulsar-admin='kubectl -n pulsar exec $(kubectl get pods --namespace pulsar -l "app=pulsar,component=bastion" -o jsonpath="{.items[0].metadata.name}") -it -- bin/pulsar-admin'
$ alias pulsar='kubectl -n pulsar exec $(kubectl get pods --namespace pulsar -l "app=pulsar,component=bastion" -o jsonpath="{.items[0].metadata.name}") -it -- bin/pulsar'
$ alias pulsar-client='kubectl -n pulsar exec $(kubectl get pods --namespace pulsar -l "app=pulsar,component=bastion" -o jsonpath="{.items[0].metadata.name}") -it -- bin/pulsar-client'
```

Check if the pulsar client has the namespace `public/default` already set up:
```sh
$ pulsar-admin namespaces list public
public/default
public/functions
```

#### Links & Resources for Pulsar
- [Installing Pulsar on Kubernetes using Helm](https://www.syscrest.com/2019/09/installing-pulsar-on-kubernetes-using-helm/)