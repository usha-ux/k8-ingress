# ingress controller

REGION_CODE=us-east-1
CLUSTER_NAME=expense
ACC_ID=745540666431


### PERMISSIONS

1 .OIDC provider 
...
eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve
...  
* IAM Policy
...
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.13.4/docs/install/iam_policy.json
...
* Create IAM role 
...
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
...
* Provide access to eks through IAM policy 
...
eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME  \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::$ACC_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve
...


### INSTALL DRIVERS 
...
* Add the EKS chart repo to Helm
...
helm repo add eks https://aws.github.io/eks-charts
...
* Installing aws load balancer controller
...
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
...