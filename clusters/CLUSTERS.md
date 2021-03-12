# Cluster resources
The deployment is made up of four resources
* ClusterDeployment, defines the OpenShift deployment for a cloud provider
* ManagedCluster, represents the management connection from ACM to the managedCluster
* MachinePool, defines the worker nodes and is able to scale the cluster
* KlusterletConfigAddon, defines the capabilities the ManagedCluster connection will have

# Example
## OpenShift on AWS
A template is provided for creating the cluster resources.
### Before you deploy
1. Modify any non-template settings in `aws-template.yaml`
2. Create a project for the cluster
```bash
oc new-project CLUSTER_NAME
```
2. Apply the two secrets `aws-cred` and `install-config`, see [SECRETS.md](../SECRETS.md)
3. Determine the available images
```bash
oc get ClusterImageSets
```
4. Create cluster command:
```bash
oc process -f aws-template.yaml -p CLUSTER_NAME=<NAME> -p CLUSTER_BASEDOMAIN=<basedomain> -p CLUSTER_RELEASE_IMAGE=<image name from above> | oc apply -f -
```
