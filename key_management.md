# Key rotation
## Standard value
Sealed secretes controller will rotate the keys every 30 days.
Old keys won't be used for new secrets but still kept to decrypt older secrets
## Change standard key renewal period
It is possible to change the key renewal period of 30 days to e.g. 120 days
Run the following cmd:
```
$ oc edit deployment sealed-secrets-controller
```
and do the changes
```
...
template:
  spec:
    containers:
    - args:
      - --key-renew-period=172800m
      command:
      - controller
...
```
To switch the automatic key rotation off, set it to `0`
```
...
template:
  spec:
    containers:
    - args:
      - --key-renew-period=0
      command:
      - controller
...
```
Restart/delete the controller pod
```
$ oc -n sealed-secrets delete pod -l name=sealed-secrets-controller
pod "sealed-secrets-controller-xxxxxxxxx-xxxxx" deleted
```
## Initiate immediate key rotation
To rotate a key immediately run:
```
$ oc -n sealed-secrets set env deployment/sealed-secrets-controller SEALED_SECRETS_KEY_CUTOFF_TIME="$(date -R)"
deployment.apps/sealed-secrets-controller updated
```
The controller pod will restart immidiately
## Key management
### List all active key (sorted by age)
```
$ oc -n sealed-secrets get secrets -l sealedsecrets.bitnami.com/sealed-secrets-key=active --sort-by='{metadata.creationTimestamp}'
```
### Set a key as compromised
```
$ oc -n sealed-secrets label secret <SECRET_NAME> sealedsecrets.bitnami.com/sealed-secrets-key=compromised
```
### Manual key renewal
- Set the key renewal period to 0
- Create a new key
- Rekey all existing sealed secrets
- Annotate the old key as compromised
- Delete the old key after a validation period

### Key backup
```
$ oc -n sealed-secrets get secret <SECRET_NAME> > key_backup.yaml.
```
### Key restore
```
$ oc create -f key_backup.yaml
```
```
$ oc -n sealed-secrets label secret <SECRET_NAME> sealedsecrets.bitnami.com/sealed-secrets-key=active
```
Restart/delete the controller pod
```
$ oc -n sealed-secrets delete pod -l name=sealed-secrets-controller
pod "sealed-secrets-controller-xxxxxxxxx-xxxxx" deleted
```
