
# Deploy the Cluster Agent on Kubernetes
[Ref Doc](https://docs.appdynamics.com/display/PRO45/Deploy+the+Cluster+Agent+on+Kubernetes)

To deploy the Cluster Agent using the AppDynamics Operator, you need a:

Cluster Agent Docker Image
Secret that contains the Controller access key
You should create the secret when you deploy the AppDynamics Operator to Kubernetes because it provides the starting configuration for the Cluster Agent.

## Create a Controller Access Key Secret
To create a secret with a Controller access key:
```
$ kubectl -n appdynamics create secret generic cluster-agent-secret --from-literal=controller-key=<access-key> --from-literal=api-user="<username>@<customer>:<password>"
```
## Create Secret to Pull the Cluster Agent from your Docker Repository
(Optional) If you are using a Docker repository that requires access credentials, create a secret to pull the Docker image from the private repository:
```
$ kubectl -n appdynamics create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=<docker-username> --docker-password=<docker-password> --docker-email=unused
```
Then, link the secret to the desired service account:
```
$ kubectl patch serviceaccount -n appdynamics appdynamics-cluster-agent -p '{"imagePullSecrets": [
{"name": "regcred"}
]}'
```

Specify this secret in your cluster-agent.yaml file using imagePullSecrets and name property:
```yaml
apiVersion: appdynamics.com/v1alpha1
kind: Clusteragent
metadata:
  name: k8s-cluster-agent
  namespace: appdynamics
spec:
  appName: "<TBA APPLICATION NAME>"
  controllerUrl: "https://morgans-pov.saas.appdynamics.com"
  account: "customer1"
  # docker image info
  image: "docker.io/appdynamics/cluster-agent:20.6.0"
  serviceAccountName: appdynamics-cluster-agent
  # enable Java auto instrumentation 
  instrumentationMethod: Env
  nsToInstrumentRegex: ecom
  defaultAppName: Ecommerce
  imageInfo:
    java:
      image: "docker.io/appdynamics/java-agent:20.3.0"
      agentMountPath: /opt/appdynamics
  ### Uncomment the following two lines if you need pull secrets
  #imagePullSecrets:
  #  name: "<your-docker-pull-secret-name>"
```

## Deploy the Cluster Agent 

Start the Cluster Agent and point it to the desired Controller. Deploy the Cluster Agent using the AppDynamics Operator:

```
$ kubectl create -f cluster-agent.yaml
```
Example output:
```
clusteragent.appdynamics.com/k8s-cluster-agent created
```

## Stop the Cluster Agent
Stop the Cluster Agent pod: 

```
$ kubectl delete -f cluster-agent.yaml
```




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