Make a Dir
mkdir k8-rbac-devloper
cd k8-rbac-devloper

1. Create a namespace called dev-namespace:

# dev-namespace.yaml
```Yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev-namespace

Apply the namespace configuration:

```Bash
kubectl apply -f dev-namespace.yaml

2. Create a ServiceAccount called dev-user:
# dev-user-sa.yaml

Apply the ServiceAccount configuration:

kubectl apply -f dev-user-sa.yaml

3. Create a Role with the required permissions for the specific namespace:
Then Apply the Role configuration:
kubectl apply -f dev-user-role.yaml

4. Create a RoleBinding to bind the Role to the ServiceAccount:
Then Apply the RoleBinding configuration:
kubectl apply -f dev-user-rolebinding.yaml

5. Generate a kubeconfig file for the user:
First, get the ServiceAccount's token:

SECRET_NAME=$(kubectl get serviceaccount dev-user -n dev-namespace -o jsonpath='{.secrets[0].name}')
TOKEN=$(kubectl get secret $SECRET_NAME -n dev-namespace -o jsonpath='{.data.token}' | base64 --decode)
or add these steps:
kubectl get secret -n dev-namespace
kubectl get secret dev-user-token-4vvkm -n dev-namespace
kubectl get secret dev-user-token-4vvkm -n dev-namespace -o jsonpath='{.data.token}' | base64 --decode
Next, get the cluster's Certificate Authority (CA) and server URL:

CA_DATA=$(kubectl get configmap -n kube-system extension-apiserver-authentication -o jsonpath='{.data.client-ca-file}')

or add these steps:
kubectl get configmap -n kube-system
kubectl get configmap extension-apiserver-authentication -n kube-system
kubectl get configmap extension-apiserver-authentication -n kube-system -o yaml
kubectl get configmap extension-apiserver-authentication -n kube-system -o jsonpath='{.data.client-ca-file}'
---

SERVER=$(kubectl config view -o jsonpath='{.clusters[0].cluster.server}')

Finally, create the kubeconfig file:
THEN Replace $CA_DATA, $SERVER, and $TOKEN with their respective values obtained in the previous steps.

Save this YAML content as dev-user-kubeconfig.yaml and provide it to the dev-user. They can now use this kubeconfig file to interact with the Kubernetes cluster, restricted to the dev-namespace namespace and the permissions defined in the dev-user-role Role.

To use the kubeconfig file, the user can set the KUBECONFIG environment variable or use the --kubeconfig flag with the kubectl command:
export KUBECONFIG=dev-user-kubeconfig.yaml
OR
kubectl --kubeconfig=dev-user-kubeconfig.yaml get pods



