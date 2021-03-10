# cluster-samples
CLI samples for creating clusters.

# Getting started
There are two core types of resource related to provisioning a cluster:  Secrets and custom resources. Since for each type there are multiple techniques for applying the kubernetes objects, we will separate the different techniques for each core type.

This gives us a folder for detailing different techniques for manging secrets and for managing ClusterDeployments with ManagedCluster custom resources.

## Secrets
- Using _*kubectl apply -f*_ (YAML) `./secrets/kubecli`
- Using a subscription to distribute secrets `./secrets/subscription`
- Using sealed secrets `./secrets/sealed/secrets`

## ClusterDeployment and ManagedCluster custom resources
- Using _oc process_ (templates) `./provision/template`
- Using _kubetctl appy -f (YAML) `./provision/kubecli`
- Using Kustomize `./proivision/kustomize`

