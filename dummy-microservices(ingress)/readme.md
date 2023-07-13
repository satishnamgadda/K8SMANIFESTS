### INGRESS- Requirements
    install AWSCLI, install kubectl
    install eksctl, create eks cluster,
    install nginx ingress controller in eks using helm,
    install ingress,ingressclass

> install eksctl,

>  #for ARM systems, set ARCH to:`arm64`, `armv6` or `armv7`
> ARCH=amd64
> PLATFORM=$(uname -s)_$ARCH
>
> curl -sLO "https://github.com/eksctl-io/eksctl/
releases/latest/download/eksctl_$PLATFORM.tar.gz"
>
> # (Optional) Verify checksum
> curl -sL "https://github.com/eksctl-io/eksctl/ releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
>
> tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm  eksctl_$PLATFORM.tar.gz
>
> sudo mv /tmp/eksctl /usr/local/bin 

>   ## create ekscluster using cluster.yaml
> 
> eksctl create cluster -f cluster.yaml
>
``` 
      apiVersion: eksctl.io/v1alpha5
      kind: ClusterConfig
      metadata: 
        name: basic-cluster 
        region: eu-north-1 
      nodeGroups: 
        - name: ng-1 
          instanceType: m5.large 
          desiredCapacity: 10 
        - name: ng-2 
          instanceType: m5.xlarge 
          desiredCapacity: 2 
```
### install nginx-ingress controller in eks using Helm
>
```
 helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

 helm repo update

 helm install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true

# Run this command to watch the Load Balancer become available:

kubectl --namespace default get services -o wide -w nginx-ingress-ingress-nginx-controller
```
> ### ingress
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecomm-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /identity(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: identity-svc
                port:
                  number: 80
```                  




     
    



