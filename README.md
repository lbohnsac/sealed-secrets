# sealed-secrets
[![](https://img.shields.io/github/release/bitnami-labs/sealed-secrets.svg)](https://github.com/bitnami-labs/sealed-secrets/releases/latest)

Sealed Secrets provide an effective means of managing secret information for your Kubernetes cluster, allowing you to check your secret information into a Version Control System (VCS) like Git. This allows you to manage your secrets via GitOps along with the rest of your cluster manifests.

Sealed Secrets is composed of two parts:

- cluster-side controller / operator
- client-side utility: kubeseal

The kubeseal utility uses asymmetric crypto to encrypt secrets that only the controller can decrypt.

The github repository for it is https://github.com/bitnami-labs/sealed-secrets

- [How to install and update sealed-secrets controller and kubeseal cmd for OCP](installation.md)
- [How to use kubeseal cmd](kubeseal_usage.md)
- [Key management](key_management.md)
