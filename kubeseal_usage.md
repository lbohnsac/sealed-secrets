# kubeseal usage
## Get the current version
To get the current installed kubeseal version run:
```
# kubeseal --version
kubeseal version: 0.33.1
```
## Environment and cmd options
If the controller is **not** installed in the namespace kube-system but in `sealed-secrets` one has to specify always this cmd option
`--controller-namespace sealed-secrets`

By setting this env var
`SEALED_SECRETS_CONTROLLER_NAMESPACE=sealed-secrets`
the cmd option isn't needed anymore
## Create a sealed-secret
### Create a sealed secret (online)
> `-w` will output yaml
>
> `>` will output json
Read the secret and write the encrypted sealed-secret
```
# kubeseal -f secret.yaml -w sealed_secret.yaml
```
```
# kubeseal -o yaml < secret.yaml > sealed_secret.yaml
```
### Create a sealed secret (offline)
First fetch the cert from the current used active key
```
# kubeseal --fetch-cert > pub-cert.pem
```
Use the fetched cert to encrypt the secret
```
# kubeseal --cert pub-cert.pem -f secret.yaml -w sealed_secret.yaml
# kubeseal --cert pub-cert.pem -o yaml < secret.yaml > sealed_secret.yaml
```
## Re-encrypt a sealed-secret with the newest active key
> [!IMPORTANT]
> To re-encrypt a sealed-secret the earlier used key has still to be active!
### Re-encrypt a sealed secret (online)
```
# kubeseal --re-encrypt -f sealed_secret.yaml -w tmp.yaml && mv tmp.yaml sealed_secret.yaml
```
### Re-encrypt a sealed secret (offline)
```
# kubeseal --cert pub-cert.pem --re-encrypt -f sealed_secret.yaml -w tmp.yaml && mv tmp.yaml sealed_secret.yaml
```
## Validate a sealed-secret
> [!IMPORTANT]
> This is only online available
```
# kubeseal --validate < sealed_secret.yaml
```
