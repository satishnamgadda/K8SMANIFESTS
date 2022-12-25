-----
kubectl apply -f <manifest.yaml>
kubectl get nodes
kubectl get pods
kubectl get pods -A (in all namespacdes)
kubectl api-resources
kubectl get po (shortname for pods)
kubectl get po -w (watch)
kubectk describe pod hello-pod (whatever the pod name is)
kubectl get po -o wide
---------------------
kubectl get rc
kubectl get po
kubectk describe po <pod-name> (whatever the pod name is)
kubectk delete po <pod-name> (whatever the pod name is)
----------------------
# for nginx-labels.yaml

kubectl get po -l app=nginx
kubectl get po -l ver=1.23
kubectl get po -l 'ver in (1)'
kubectl get po -l 'ver notin (1)'
kubectl get po -l 'ver in (1.23,1.24)'
kubectl get po -l 'ver in (1.23,1.24),app in (apache,nginx)'
------------------------------
# ReplicaSet
kubectl apply -f <manifest.yaml>
kubectl get rs
kubectl get po -o wide -w
kubectl delete po <pod-name>
# (here Recounsellation loop comes into play and maintains desired state)
# scaling number of replicas
# Imperative way
kubectl scale --help
kubectl scale rs <rs-name> --replicas=<number>
kubectl get po
# declarative way
# change the spec in manifest and apply manifest again
kubectl apply -f <manifest.yaml>
------------------------------------
### SVC
kubectl apply -f <manifest.yaml>
kubectl get rs
kubectl get po -o wide -w
kubectl api-resources | grep service
kubectl get svc
kubectl get svc -o wide
kubectl exec -it <exp-pod> -- /bin/sh
/# curl http://<IP>:35000
/# apk add curl
/# curl http://<nginx-svc>:35000
# for nginx-svc1.yaml
nodePort
the range of valid port is 30000-32767
# for nginx-cluster-svc.yaml
kubectl get svc
kubectl get ep
kubectl get endpointslices
kubectl describe endpoints nginx-svc
-----------------------------------------------
# # # DEPLOYMENTS
kubectl get deploy
kubectl get rs
kubectl get pods
kubectl rollout --help
kubectl rollout status deployments/jenkins-deploy
kubectl rollout history deployments/jenkins-deploy
kubectl get svc
# here change jenkins image to other version 2.60.3
kubectl apply -f <manifest.yaml>  (jenkins-deploy.yaml)
kubectl rollout status deployments/jenkins-deploy
# now we use http://<ip>:38000
  still old version is running
    after new version updated , changed to new version
kubectl rollout undo --help
kubectl rollout undo deployments/jenkins-deploy --to-revision=1
------------------------------------------------------------
### for Job&CronJob
kubectl apply -f <manifest.yaml>
kubectl get namespace
kubectl get jobs
kubectl get jobs --namespace my-namespace
kubectl get jobs --namespace my-namespace -w
kubectl describe jobs demo-job --namespace my-namespace
kubectl get Cronjobs --namespace my-namespace
kubectl get jobs --namespace my-namespace
--------------------
### VOLUMES
kubectl apply -f <manifest.yaml>
kubectl get pods
kubectl describe pods mysql-vol-demo
kubectl exec -it mysql-vol-demo -- mysql -u root -p
  mysql> show databases;
  mysql> use employees;
  mysql> CREATE TABLE persons;
  mysql> select * from employees persons;
# insert data into table
kubectl delete -f <manifest.yaml>
(kubectl delete -f mysql-vol-demo.yaml)  
kubectl delete -f mysql-vol-demo.yaml
kubectl exec -it mysql-vol-demo -- mysql -u root -p
mysql> use employees;
mysql> select * from employees persons;
# volumes cannot persist the data above or beyond the lifecycle of pod.
# so we need to use persistent volumes
--------------------------------------------
### ConfigMaps
kubectl apply -f hello-config.yaml
kubectl apply -f pod-config-env.yaml
kubectl get cm
kubectl exec -it pod-config-env -- /bin/sh
/# echo $log_level
o/p: DEBUG
/# echo $retries
o/p: 3
/# echo $timeout
o/p: 30s
/# printenv
# we have created ConfigMaps and injected as ENV variables
kubectl apply -f pod-config-mount.yml
kubectl exec -it pod-config-mount -- /bin/sh
/# /configs
/# cat /configs/log_level
DEBUG/#
#  we have created ConfigMaps as volume mounts,
  in this we can change the configmap values dynamically
-------------------------------------------------  
### Secrets
kubectl apply -f pod-secret.yml
kubectl apply -f pod-secret-mount.yml
kubectl exec -it pod-secret-mount -- /bin/sh
/# /secrets
/# cat /secrets/username
/# cat /secrets/password
qtdevops/#
---------------------------------------------------------
### taint and tolerations
kubectl taint nodes <node-name> POC=true:NoSchedule
kubectl describe nodes
kubectl apply -f podexample.yaml
kubectl get po -o wide
# ensure all the nodes are tainted and apply
(you can also schedule pods on specific node by nodeselector property in pod spec which matches exact labels)
kubectl apply -f otherpod.yaml
kubectl get po -o wide
---------------------------------
### hpa
kubectl apply -f nginx-deploy.yaml
kubectl apply -f nginx-hpa.yaml
kubectl get hpa
# here if we cross the cpu utilization , the scaling will be done
# we can scale by using metrics property in pod spec
----------------------------------------------------------------



