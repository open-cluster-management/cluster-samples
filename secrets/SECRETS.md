# Hive Secrets
Hive uses four pieces of sensitive data that can be stored in two secrets:
  1. Cloud Provider Credential - Used to connect to the Cloud provider
  2. Private ssh key - Used to connect to the cluster nodes
  3. Public ssh key - Placed on each node so the Private ssh key will work
  4. OpenShift Pull secret

## Defining the secrets
First copy the `secret-template.yaml` to `secret.yaml`
```bash
cp secret-template.yaml secret.yaml
```
Edit the secret.yaml, inside you will need to provide a value for the following keys
- aws_access_key_id         # AWS ID
- aws_secret_access_key     # AWS secret
- .dockerconfigjson         # OpenShift pull secret (from redhat.com subscriptions)
- ssh-private-key           # An SSH Private key
- sshKey                    # The SSH Public key for the Private key you chose

### To apply the secret:
1. Create the project `oc new-project CLUSTER_NAME`
2. `oc process -f ./secret.yaml -p CLUSTER_NAME=<cluster name> -p CLUSTER_BASEDOMAIN=<cluster base domain> | oc apply -f -`



## Raw secret for DIY
1. Sensitive secret.
```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: CLUSTER_NAME
  namespace: CLUSTER_NAME
stringData:
  .dockerconfigjson: |-
    {"auths":{"cloud.openshift.com":{"auth":"","email":"name@email.com"},"quay.io":{"auth":"","email":"name@email.com"},"registry.connect.redhat.com":{"auth":"","email":"name@email.com"},"registry.redhat.io":{"auth":"","email":"name@email.com"}}}
  aws_access_key_id: 
  aws_secret_access_key: 
  ssh-privatekey: |-
    -----BEGIN RSA PRIVATE KEY-----
    -----END RSA PRIVATE KEY-----
type: Opaque
```
## What is inside
* Infrastructure provider credentials
  - `aws_access_key_id`
  - `aws_secret_access_key`
* OpenShift Pull secret (obtained from your subscriptions @redhat.com)
  - `.dockerconfigjson`
* Private-ssh-key used to connect to the provisioned OpenShift cluster nodes
  - `ssh-privatekey`

2. Install Config secret
```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: CLUSTER_NAME-install-config
  namespace: CLUSTER_NAME
type: Opaque
stringData:
  install-config.yaml: |-
    apiVersion: v1
    metadata:
      name: CLUSTER_NAME
    baseDomain: BASE_DOMAIN
    controlPlane:
      hyperthreading: Enabled
      name: master
      platform:
        aws:
          rootVolume:
            iops: 100
            size: 100
            type: gp2
          type: m5.xlarge
      replicas: 3
    compute:
    - hyperthreading: Enabled
      name: worker
      platform:
        aws:
          rootVolume:
            iops: 100
            size: 100
            type: gp2
          type: m5.xlarge
      replicas: 3
    networking:
      clusterNetwork:
      - cidr: 10.128.0.0/14
        hostPrefix: 23
      machineCIDR: 10.0.0.0/16
      networkType: OpenShiftSDN
      serviceNetwork:
      - 172.30.0.0/16
    platform:
      aws:
        region: us-east-1
    pullSecret: "" # skip, hive will inject based on it's secrets
    sshKey: |-
      ssh-rsa ... your_email@example.com
```
* Replace `CLUSTER_NAME`, `BASE_DOMAIN` and `sshKey` (this is the public-ssh-key for the private-ssh-key used in the other secret)
* Customize other settings you would like to persist