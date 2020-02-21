# Vault Helm Chart

This repository contains the official HashiCorp Helm chart for installing
and configuring Vault on Kubernetes. This chart supports multiple use
cases of Vault on Kubernetes depending on the values provided.

For full documentation on this Helm chart along with all the ways you can
use Vault with Kubernetes, please see the
[Vault and Kubernetes documentation](https://www.vaultproject.io/docs/platform/k8s/index.html).

## Prerequisites Notes

To use the charts here, [Helm](https://helm.sh/) must be installed in your
Kubernetes cluster. Setting up Kubernetes and Helm and is outside the scope
of this README. Please refer to the Kubernetes and Helm documentation.

The versions required are:

  * **Helm 2.10+** - This is the earliest version of Helm tested. It is possible
    it works with earlier versions but this chart is untested for those versions.
  * **Kubernetes 1.9+** - This is the earliest version of Kubernetes tested.
    It is possible that this chart works with earlier versions but it is
    untested. Other versions verified are Kubernetes 1.10, 1.11.

## Usage

For now, Hashicorp do not host a chart repository. To use the charts, you must
download their repository and unpack it into a directory. Either
[download a tagged release](https://github.com/hashicorp/vault-helm/releases) or
use `git checkout` to a tagged release.

Repository was unpacked into a directory `vault`, unneeded files were removed and the chart can
then be installed directly (remember to provide a release name e.g. vault):

    helm install [release-name] ./

For example I called my release deathstar

```sh
# command to install the chart
$ helm install deathstar ./

# to initialise the vault
$ kubectl exec -ti deahtstar-vault-0 -- vault operator init
Unseal Key 1: 6Q8TlQD2Mz1JoUIE2dqWJFFqIMqSfnbMb8tHZ7CIN9NB
Unseal Key 2: 8p6cnnMThssTPTctLGSOp7n3SzT3It3NwtqMK+mVgXNq
Unseal Key 3: Ox1W3F1t4nXcz8RI7P2jbF/Fz93c1zZ3cSqwBFf3I6RF
Unseal Key 4: DevYyVTy4+E2GpXTW0tc+mjA8vFJPwvy9C1+A9c2Ro+H
Unseal Key 5: AoE5txPBsCdX7C1mXoK5zcLFdGmjlIQFeuTuICgoKtUK

Initial Root Token: s.8ViKIwLpuHdqdmFKU73MI7BA

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 3 key to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.

# unseal the vault
$ kubectl exec -ti deathstar-vault-0 -- vault operator unseal
Unseal Key (will be hidden):
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    1/3
Unseal Nonce       607ff6a6-ebc0-c239-ceb8-0c78ea7978a2
Version            1.3.1
HA Enabled         false

# after entering 3 unseal keys (repeat above with diff keys) you get following:
Unseal Key (will be hidden):
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    5
Threshold       3
Version         1.3.1
Cluster Name    vault-cluster-ac7cf63b
Cluster ID      3451fe36-10a1-ba68-ed5d-3fd204911586
HA Enabled      false
# note the cluster Name has now been revealed

# viewing the vault
# Login using the token that was provided when initialising
$ kubectl port-forward deathstar-vault-0 8200:8200
```
Note:
1. In the above examples the release was called deathstar
2. I am running minikube on mac for my kubernetes cluster
3. minikube also works with windows and uses hyperkit or virtualbox in background

Please see the many options supported in the `values.yaml`
file. These are also fully documented directly on the
[Vault website](https://www.vaultproject.io/docs/platform/k8s/helm.html).

#### Links
- [Running Vault on Kubernetes](https://www.vaultproject.io/docs/platform/k8s/helm/run/)