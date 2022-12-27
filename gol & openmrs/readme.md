--------------------------
OPENMRS & GAMEOFLIFE

---Dockerfile(multi-stage OPENMRS)
FROM maven:3-jdk-8 as build
RUN git clone https://github.com/satishnamgadda/openmrs-core.git && \
    cd openmrs-core && \
    mvn package

# Dockerfile ,,,Openmrs run i,e stage-2
#war file location: /openmrs-core/webapp/target/openmrs.war
FROM tomcat:8-jdk8
LABEL application="openmrs"
LABEL owner="me"
COPY --from=build /openmrs-core/webapp/target/openmrs.war /usr/local/tomcat/webapps/openmrs.war
EXPOSE 8080
CMD ["catalina.sh", "run"]

---Dockerfile(multi-stage GAMEOFLIFE)
FROM maven:3.8.6-openjdk-8 as build
RUN git clone https://github.com/satishnamgadda/game-of-life.git && \
    cd game-of-life && \
    mvn package

# Dockerfile ,,,Gameoflife run i,e stage-2
#war file location: /game-of-life/gameoflife-web/target/gameoflife.war
FROM tomcat:8-jdk8
LABEL application="gameoflife"
LABEL owner="me"
COPY --from=build /game-of-life/gameoflife-web/target/gameoflife.war /usr/local/tomcat/webapps/gameoflife.war
EXPOSE 8080
CMD ["catalina.sh", "run"]

# Declarative-pipeline for GAMEOFLIFE
pipeline {
    agent { label 'build' }
    stages {
        stage('vcs') {
            steps {
                git url: "https://github.com/satishnamgadda/game-of-life.git",
                    branch: "REL_INT_1.0"
            }
        }
        stage('buildstage') {
            steps {
                sh 'docker info'
                sh 'docker image build -t satish1990/gol:1.0 .'
            }
        }
        stage('push') {
            steps {
                sh 'docker image push satish1990/gol:1.0'
            }
        }
    }
}

# Declarative-pipeline for OPENMRS
pipeline {
    agent { label 'build' }
    stages {
        stage('vcs') {
            steps {
                git url: "https://github.com/satishnamgadda/openmrs-core.git",
                    branch: "master"
            }
        }
        stage('buidstage') {
            steps {
                sh 'docker info'
                sh 'docker image build -t satish1990/openmrs:1.0 .'
                
            }
        }
        stage('push') {
            steps {
                sh 'docker image push satish1990/openmrs:1.0'
            }
        }
    }
}
#  images in docker-hub
![preview](/Images/mydockerhub.png)

# Deployment and  service manifests for OPENMRS
# ---openmrs-deploy.yml
---
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: openmrs-deploy
spec:
  minReadySeconds: 5
  replicas: 1
  selector:
    matchLabels:
      app: openmrs
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      name: openmrs
      labels:
        app: openmrs
    spec:
      containers:
        - name: openmrs-pod 
          image: satish1990/openmrs:1.0         
          ports:
            - containerPort: 8080
              protocol: TCP
          command:
            - catalina.sh
            - run    
# -----openmrs-svc.yaml
---
apiVersion: v1
kind: Service
metadata:
  name: openmrs-svc
spec:
  type: LoadBalancer
  selector:
    app: openmrs
    app1: gol
  ports:
    - name: webport
      port: 33000
      targetPort: 8080
    - name: webport1
      port: 31000  
      targetPort: 8080

# Deployment and  service manifests for Gameoflife
# ---gol-deploy.yml
---
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: gol-deploy
spec:
  minReadySeconds: 5
  replicas: 1
  selector:
    matchLabels:
      app1: gol
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      name: gol
      labels:
        app1: gol
    spec:
      containers:
        - name: gol-pod 
          image: satish1990/gol:1.0         
          ports:
            - containerPort: 8080
              protocol: TCP
          command:
            - catalina.sh
            - run    
# ---  gol-svc.yaml
---
apiVersion: v1
kind: Service
metadata:
  name: gol-svc
spec:
  type: LoadBalancer
  selector:
    app1: gol
  ports:
    - name: webport
      port: 33000
      targetPort: 8080
# k8s commands for GAMEOFLIFE
kubectl apply -f gol-deploy.yml
kubectl apply -f gol-svc.yml
kubectl get deploy
kubectl get po
kubectl get svc
kubectl get rs 
# k8s commands for OPENMRS
kubectl apply -f openmrs-deploy.yml
kubectl apply -f openmrs-svc.yml
kubectl get deploy
kubectl get po
kubectl get svc
kubectl get rs 
# here we are getting DNS, accessed with DNS
http://<dnsnmae>:<portnumber>/openmrs
http://<dnsname>:<portnumber>/gameoflife

![preview](/Images/k8s1.png)
![preview](/Images/gol.jpg) 

        






