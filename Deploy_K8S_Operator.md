
# Deploy the AppDynamics Operator on Kubernetes


[Reference Doc](https://docs.appdynamics.com/display/PRO45/Deploy+the+AppDynamics+Operator+on+Kubernetes)

Skip to end of metadataGo to start of metadata
To deploy the AppDynamics Operator on Kubernetes, open a command prompt and follow these steps:


This page contains links to Kubernetes documentation. AppDynamics makes no representation as to the accuracy of Kubernetes documentation because Kubernetes controls its own documentation.

Ensure your kubectl is pointing to your Kubernetes cluster. See Install and Set Up kubectl in the Kubernetes documentation

Log on as a cluster administrator.

To create a namespace for appdynamics in your Kubernetes cluster, enter:
```
$ kubectl create namespace appdynamics
```

To verify your namespace is created, enter:
```
$ kubectl get namespace
Example output:

NAME                STATUS          AGE
appdynamics         Active          5s
default             Active          41d
kube-node-lease     Active          41d
kube-public         Active          41d
kube-system         Active          41d
```
To install the latest version of the AppDynamics Operator for Kubernetes, enter:
```
$ kubectl create -f cluster-agent-operator.yaml
```
The AppDynamics Operator creates a Custom Resource Definition, which extends the Kubernetes APIs to create a new Kubernetes type called clusteragents. The AppDynamics Operator also creates a serviceaccount, a clusterrole, and clusterrolebinding for the namespace.

Example output: 
```
customresourcedefinition.apiextensions.k8s.io/clusteragents.appdynamics.com created
customresourcedefinition.apiextensions.k8s.io/infravizs.appdynamics.com created
serviceaccount/appdynamics-operator created
serviceaccount/appdynamics-infraviz created
podsecuritypolicy.extensions/appdynamics-infraviz created
clusterrole.rbac.authorization.k8s.io/appdynamics-operator created
clusterrolebinding.rbac.authorization.k8s.io/appdynamics-operator created
clusterrolebinding.rbac.authorization.k8s.io/appdynamics-infraviz created
role.rbac.authorization.k8s.io/appdynamics-infraviz created
rolebinding.rbac.authorization.k8s.io/appdynamics-infraviz created
deployment.apps/appdynamics-operator created

```

To verify that the AppDynamics Operator is running, enter:
```
kubectl -n appdynamics get pods
```
Example output:
```
NAME                                    READY       STATUS      RESTARTS    AGE
appdynamics-operator-95ffb549c-m8tjv    1/1         Running     0           30s
```
This output shows that Kubernetes extends the custom resource definitions that you provided. You can now Build the Cluster Agent Container Image.