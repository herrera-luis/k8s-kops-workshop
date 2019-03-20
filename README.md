In mac install

```
brew install kops
```


Create the kops IAM user from the command line using the following:

```bash
aws iam create-group --group-name kops

aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops

aws iam create-user --user-name kops

aws iam add-user-to-group --user-name kops --group-name kops

aws iam create-access-key --user-name kops
```

You should record the SecretAccessKey and AccessKeyID in the returned JSON
output, and then use them below:

```bash
# configure the aws client to use your new IAM user
aws configure           # Use your new access and secret key here
aws iam list-users      # you should see a list of all your IAM users here
```


```bash
# Because "aws configure" doesn't export these vars for kops to use, we export them now
export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
```

Note: If you are using Kops 1.6.2 or later, then DNS configuration is
optional. Instead, a gossip-based cluster can be easily created. The
only requirement to trigger this is to have the cluster name end with
`.k8s.local`. If a gossip-based cluster is created then you can skip
this section.

Create a bucket to store the state of kops

```bash
aws s3api create-bucket --bucket uio.k8s.cluster.thoughtworks.com --region us-east-1
```

For a gossip-based cluster, make sure the name ends with `k8s.local`. For example:

```bash
export NAME=uio.k8s.cluster.thoughtworks.com.k8s.local
export KOPS_STATE_STORE=s3://uio.k8s.cluster.thoughtworks.com
```

Create a cluster:

First step

```bash
kops create cluster --zones us-east-2a $NAME
```

You can edit your configuration

```bash
kops edit cluster ${NAME}
```

apply and create our cluster

```bash
kops update cluster ${NAME} --yes
```

View the estatus of the cluster

```bash
kops validate cluster
```

when the cluster is healthy and up you can see the node of the cluster

```bash
kubectl get nodes
```

you can see the components of the cluster

```bash
kubectl -n kube-system get po
```

finally you can delete de cluster

this is to view
```bash
kops delete cluster --name ${NAME}
```

and this is to apply 
```bash
kops delete cluster --name ${NAME} --yes
```

in this steps you have to create the kube config and you should to add your like this:

```bash
kops create secret --name uio.k8s.cluster.thoughtworks.com.k8s.local sshpublickey admin -i ~/.ssh/xxxxx.pub
```
