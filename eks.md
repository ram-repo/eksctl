# RBC for EKS CLUSTER

## read only permissions for eks
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "eks:DescribeCluster",
                "eks:ListClusters"
            ],
            "Resource": "*"
        }
    ]
}
```
1. Create an IAM User and Add to Group:
2. Create a new IAM user named developer1.
3. Select "Programmatic access" for access type.
4. Add the user to the developer group.
5. Save the access key ID and secret access key.

# Step 2: Configure RBAC in the EKS Cluster
1. Update aws-auth ConfigMap:
2. Update the aws-auth ConfigMap in your EKS cluster to map the IAM user to a Kubernetes user.
3. Create a file named aws-auth-patch.yaml with the following content:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: arn:aws:iam::<AWS_ACCOUNT_ID>:user/developer1
      username: developer1
      groups:
        - developer

```
## Apply the ConfigMap:

```sh
kubectl apply -f aws-auth-patch.yaml
```
## Create a Read-Only Role and RoleBinding:

## Create a file named readonly-role.yaml with the following content:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: read-only
rules:
- apiGroups: [""]
  resources: ["pods", "services", "deployments"]
  verbs: ["get", "list", "watch"]
```
##  Create a file named `readonly-rolebinding.yaml` with the following content:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-only-binding
  namespace: default
subjects:
- kind: User
  name: developer1
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: read-only
  apiGroup: rbac.authorization.k8s.io
```
## - Apply the files to the cluster:
```sh
kubectl apply -f readonly-role.yaml
kubectl apply -f readonly-rolebinding.yaml
```
## Step 3: Test the Access
1. Configure kubectl for the IAM User:
Set up AWS CLI with the IAM user credentials.
```sh
aws configure
```
# Enter the access key ID and secret access key for developer1
## Update kubeconfig:
```sh
aws eks update-kubeconfig --name <cluster_name> --region <region> --role-arn arn:aws:iam::<AWS_ACCOUNT_ID>:role/<role_name>
```
