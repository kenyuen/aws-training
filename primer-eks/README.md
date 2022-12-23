# EKS Primer

```shell
## creates default cluster with random naming
eksctl create cluster

## use the following to enable/disable logging
eksctl utils update-cluster-logging --enable-types=all --region=us-east-2 --cluster={cluster}
eksctl utils update-cluster-logging --disable-types=all --region=us-east-2 --cluster={cluster} --approve

## add storage policy
kubectl get pv,all -A
curl -o example-iam-policy.json  https://raw.githubusercontent.com/kubernetes-sigs/aws-ebs-csi-driver/master/docs/example-iam-policy.json
aws iam create-policy --policy-name EBS_CSI_Driver_Policy --policy-document file://example-iam-policy.json
eksctl create iamserviceaccount \
    --name ebs-csi-controller-sa \
    --namespace kube-system \
    --cluster {cluster} \
    --attach-policy-arn arn:aws:iam::{accountid}:policy/EBS_CSI_Driver_Policy \
    --approve \
    --override-existing-serviceaccounts

## sample pod add to simulate load to sample logging and insights from CloudWatcgh
kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
kubectl run -it load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O - http://php-apache; done"

## delete cluster
eksctl delete cluster --region=us-east-2 --name={cluster}



```