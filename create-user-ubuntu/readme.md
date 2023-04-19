## **To create a dev-user on an Ubuntu system and provide them with the kubeconfig file, follow these steps:**
```bash
# 1. Create the dev-user on the Ubuntu system:
sudo adduser dev-user

This will create a new user called dev-user and prompt you to set a password for the new user.

2. Switch to the dev-user account:
sudo su - dev-user

and Add the new user to the sudo group to grant them administrative privileges:
sudo usermod -aG sudo newuser


3. Create a .kube directory in the dev-user home folder:
mkdir ~/.kube

4. Copy the kubeconfig file you created for the dev-user (e.g., dev-user-kubeconfig.yaml) to the .kube directory:

# Switch back to your original user account in another terminal or using 'exit' command
cp /path/to/dev-user-kubeconfig.yaml /home/dev-user/.kube/config

Make sure to replace /path/to/dev-user-kubeconfig.yaml with the actual path to the file you created previously.

5. Change the ownership of the copied kubeconfig file to dev-user:
sudo chown dev-user:dev-user /home/dev-user/.kube/config

Now, the dev-user on the Ubuntu system has their kubeconfig file set up. When logged in as dev-user, they can use kubectl commands to interact with the Kubernetes cluster with the permissions and namespace specified in the kubeconfig file:

# As dev-user
kubectl get pods

The dev-user will only have access to the resources and actions specified by the Role and RoleBinding created in the Kubernetes cluster.

