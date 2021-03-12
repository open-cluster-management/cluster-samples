# Hive Secrets
Hive uses four pieces of sensitive data that can be stored in two secrets:
  1. Cloud Provider Credential - Used to connect to the Cloud provider
  2. Private ssh key - Used to connect to the cluster nodes
  3. Public ssh key - Placed on each node so the Private ssh key will work
  4. OpenShift Pull secret

## Defining the secrets
1. First copy the `secret-template.yaml` to `secret.yaml`
```bash
cp secret-template.yaml secret.yaml
```
2. Edit the secret.yaml, inside you will need to provide a value for the following keys
- `aws_access_key_id`         # AWS ID
- `aws_secret_access_key`     # AWS secret
- `.dockerconfigjson`         # OpenShift pull secret (from redhat.com subscriptions)
- `ssh-private-key`           # An SSH Private key
- `sshKey`                    # The SSH Public key for the Private key you chose
3. Create the project
```bash
oc new-project CLUSTER_NAME
```
4. Create the secrets
```bash
oc process -f ./secret.yaml -p CLUSTER_NAME=<cluster name> -p CLUSTER_BASEDOMAIN=<cluster base domain> | oc apply -f -
```
