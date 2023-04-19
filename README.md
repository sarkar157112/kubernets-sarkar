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

Next, get the cluster's Certificate Authority (CA) and server URL:

CA_DATA=$(kubectl get configmap -n kube-system extension-apiserver-authentication -o jsonpath='{.data.client-ca-file}')
SERVER=$(kubectl config view -o jsonpath='{.clusters[0].cluster.server}')

Finally, create the kubeconfig file:



