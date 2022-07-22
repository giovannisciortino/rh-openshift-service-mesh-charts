
# RH OpenShift Service Mesh Charts

This folder contains two helm charts:

## 1) rhossm-operator

This chart installs the Red Hat Service Mesh operator and the 3 operators needed by Red Hat Service Mesh (Kiali, ElasticSearch, Jaeger).
This chart installs thee operator with the parameter "installPlanApproval: Manual" requiring a manual approval for the upgrade of the operator.
During the first installation of the operators the approval of the related InstallPlan is approved automatically using an ArgoCD hook for each operator to search the generated InstallPlan and approve it.
Only an instance of this chart can be installed on each OpenShift cluster.

The values.yaml file contains the channels,namespaces,versions of each operator and currently it is unique for all the ezBob environment (svil, test, prod).

| IMPORTANT NOTE: |
|--|
|Before to apply this chart verify if exists operators already installed with “installPlanApproval: Manual” in the following namespaces:  
 openshift-operators-redhat
 openshift-distributed-tracing
 openshift-operators
 redhat-operators
|
For the expected behaviour described in [2], the automatic InstallPlan approval of the 4 operators installed by this chart approve also any pending operator upgrade(if available) in the namespaces listed above. If there are any pending operator upgrades verify the impact of the upgrade of these operators and sync this chart during a maintenance windows if it's needed.


## 2) rhossm-instance

This chart create a new mesh of Red Hat Service Mesh.
It create the namespace of the new mesh, a ServiceMeshControlPlane resource and a ServiceMeshMemberRoll.

If it is neeeded multple instance of this chart can be installed on a OpenShift cluster in order to configure multiple mesh of Red Hat Service Mesh in the same cluster.

A different values.yaml files has been created for each ezbob cluster:
1) values-vcptoisp003s-svil.yaml
2) values-vcptoisp003t-test.yaml
3) values-vcptoisp003p-prod.yaml

The values files of this charts allows to configure:

- The namespace of the new mesh
- The ServiceMeshControlPlane spec (this is currently equal on all the three values files ( values-vcptoisp003s-svil.yaml , values-vcptoisp003t-test.yaml and values-vcptoisp003p-prod.yaml )
- The ServiceMeshMemberRoll spec containing the list of the namespaces that will be included in the new mesh

| IMPORTANT NOTE: |
|--|
|The namespaces included in ServiceMeshMemberRoll spec must be already created before that ArgoCD can include them ServiceMeshMemberRoll resource. If the namespaces specified in ServiceMeshMemberRoll spec are created after, resync the ServiceMeshMemberRoll resource



[1] https://redhat-scholars.github.io/argocd-tutorial/argocd-tutorial/04-syncwaves-hooks.html#exploring_resource_hooks
[2] https://access.redhat.com/solutions/6389681
