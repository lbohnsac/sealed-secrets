# Sealed-secrets controller
[![](https://img.shields.io/github/release/bitnami-labs/sealed-secrets.svg)](https://github.com/bitnami-labs/sealed-secrets/releases/latest)

Sealed Secrets provide an effective means of managing secret information for your Kubernetes cluster, allowing you to check your secret information into a Version Control System (VCS) like Git. This allows you to manage your secrets via GitOps along with the rest of your cluster manifests.

Sealed Secrets is composed of two parts:

- cluster-side controller / operator
- client-side utility: kubeseal

The kubeseal utility uses asymmetric crypto to encrypt secrets that only the controller can decrypt.

The github repository for it is https://github.com/bitnami-labs/sealed-secrets

## Get the latest version info
```bash
$ curl -sSL "https://api.github.com/repos/bitnami-labs/sealed-secrets/releases/latest" | grep -Po '"tag_name": "\K.*?(?=")'
v0.33.1
```
## Download and fix the controller.yaml for openshift
> [!IMPORTANT]
> Due to the default high security settings in OpenShift will the default yaml config not run on OpenShift.\
> The issues are the `runAsUser` and `fsGroup`.\
> We also install the controller in the `sealed-secrets` namespace instead of `kube-system`
```bash
$ SEALED_SECRET_VERSION=$(curl -sSL "https://api.github.com/repos/bitnami-labs/sealed-secrets/releases/latest" | grep -Po '"tag_name": "\K.*?(?=")')
$ curl -sSL https://github.com/bitnami-labs/sealed-secrets/releases/download/${SEALED_SECRET_VERSION:?}/controller.yaml |sed 's/namespace: kube-system/namespace: sealed-secrets/g' | sed -E '/.*(fsGroup|runAsUser):.*/d' > ./manifests/${SEALED_SECRET_VERSION:?}-openshift.yaml
```
- This will get the latest version
- Download the `controller.yaml`
- Replace in the `controller.yaml` `kube-sytem` with `sealed-secrets`
- Delete in the `controller.yaml` `fsGroup and `runAsUser`
- Write the resulting file to the subdirectory `manifests`

## Configure the kustomization.yaml in the subdirectory `manifests` to the downloaded version
```bash
$ cat ./manifests/kustomization.yaml
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ns_sealed-secrets.yaml
  - v0.33.1-openshift.yaml
...
```

## Update the controller
To update the controller pull the newer version and deploy it. There's no need to uninstall anything before.

# Kubeseal
Kubeseal is the cmd tool that encrypts secrets so they can be stored securely in git.

## Kubeseal binary download
The latest kubeseal client can be installed on Linux, using the below commands:
```bash
$ KUBESEAL_VERSION=$(curl - "https://api.github.com/repos/bitnami-labs/sealed-secrets/releases/latest" | grep -Po '"tag_name": "\K.*?(?=")')
$ curl -OL "https://github.com/bitnami-labs/sealed-secrets/releases/download/${KUBESEAL_VERSION:?}/kubeseal-${KUBESEAL_VERSION#v}-linux-amd64.tar.gz"
$ tar -xvzf kubeseal-${KUBESEAL_VERSION#v}-linux-amd64.tar.gz kubeseal
$ sudo install -m 755 kubeseal /usr/local/bin/kubeseal
$ rm kubeseal-${KUBESEAL_VERSION#v}-linux-amd64.tar.gz kubeseal
```
- This will get the latest version
- Download the tar ball
- Extract only the kubeseal binary out of the tar ball
- Install the kubeseal binary
- And finally cleanup
