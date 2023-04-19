## To create a remote user with limited access to a specific namespace in Kubernetes, you will need to follow these steps:

1. Create a namespace, if it doesn't already exist.
2. Create a ServiceAccount for the remote user.
3. Create a Role with the required permissions for the specific   namespace.
4. Create a RoleBinding to bind the Role to the ServiceAccount.
5. Generate a kubeconfig file for the remote user using the ServiceAccount's token.

# 1. Create a namespace called my-namespace:

Create the `my-namespace.yaml` file with the following content:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace


Apply the namespace configuration:


kubectl apply -f my-namespace.yaml


2. Create a ServiceAccount called remote-user:

Create the `remote-user-sa.yaml` file with the following content:

apiVersion: v1
kind: ServiceAccount
metadata:
  name: remote-user
  namespace: my-namespace

3. Create a Role with the required permissions for the specific namespace:
# remote-user-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: remote-user-role
  namespace: my-namespace
rules:
- apiGroups: [""]
  resources: ["pods", "services", "deployments", "configmaps"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]

Apply the Role configuration:
kubectl apply -f remote-user-role.yaml

4. Create a RoleBinding to bind the Role to the ServiceAccount:
# remote-user-rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: remote-user-rolebinding
  namespace: my-namespace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: remote-user-role
subjects:
- kind: ServiceAccount
  name: remote-user
  namespace: my-namespace

Apply the RoleBinding configuration:
kubectl apply -f remote-user-rolebinding.yaml

5. Generate a kubeconfig file for the remote user:
First, get the ServiceAccount's token:
SECRET_NAME=$(kubectl get serviceaccount remote-user -n my-namespace -o jsonpath='{.secrets[0].name}')
TOKEN=$(kubectl get secret $SECRET_NAME -n my-namespace -o jsonpath='{.data.token}' | base64 --decode)

Next, get the cluster's Certificate Authority (CA) and server URL:

CA_DATA=$(kubectl get configmap -n kube-system extension-apiserver-authentication -o jsonpath='{.data.client-ca-file}')
SERVER=$(kubectl config view -o jsonpath='{.clusters[0].cluster.server}')

Finally, create the kubeconfig file:
# remote-user-kubeconfig.yaml
apiVersion: v1
kind: Config
clusters:
- name: my-cluster
  cluster:
    certificate-authority-data: $CA_DATA
    server: $SERVER
contexts:
- name: remote-user-context
  context:
    cluster: my-cluster
    namespace: my-namespace
    user: remote-user
users:
- name: remote-user
  user:
    token: $TOKEN
current-context: remote-user-context

Replace $CA_DATA, $SERVER, and $TOKEN with their respective






