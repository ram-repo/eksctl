# eksctl


# create eks cluster without node group

```bash
eksctl create cluster --name dev-cluster --version 1.30 --region us-east-1  --with-oidc --without-nodegroup
```

# create node group
```bash
eksctl create nodegroup `
  --cluster dev-cluster `
  --region us-east-1 `
  --name worknodegroup1 `
  --node-type t2.large `
  --nodes 2 `
  --nodes-min 1 `
  --nodes-max 2 `
  --ssh-access `
  --ssh-public-key Dell_laptop
```

# get kube config
```bash
aws eks update-kubeconfig --name dev-cluster --region us-east-1
```

# delete node group
```bash
eksctl delete nodegroup --cluster dev-cluster --name worknodegroup1
```

# Delete cluster
```bash
eksctl delete cluster --name dev-cluster
```
