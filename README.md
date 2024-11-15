# INSTALL KUBECTL
sudo apt-get update
sudo apt-get install -y kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
export PATH=$PATH:/usr/local/bin

# INSTALL MINIKUBE 
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version

# CREATE NAMESPACE AND SET AS DEFAULT
kubectl create ns k8-project
kubectl config set-context --current --namespace k8-project

# MONGO Database Setup 
 > To create Mongo statefulset with Persistent volumes:
    kubectl apply -f mongo-statefulset.yaml

> To create Mongo service:
   kubectl apply -f mongo-service.yaml

> To get inside amongo-0 pod:
   kubectl exec -it mongo-0 -- mongo

> On the mongo-0 pod, initialise the Mongo database Replica set. In the terminal run the following command:
  rs.initiate();
  sleep(2000);
  rs.add("mongo-1.mongo:27017");
  sleep(2000);
  rs.add("mongo-2.mongo:27017");
  sleep(2000);
  cfg = rs.conf();
  cfg.members[0].host = "mongo-0.mongo:27017";
  rs.reconfig(cfg, {force: true});
  sleep(5000);

> load thedata in database:
  use langdb;
db.languages.insert({"name" : "csharp", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 5, "compiled" : false, "homepage" : "https://dotnet.microsoft.com/learn/csharp", "download" : "https://dotnet.microsoft.com/download/", "votes" : 0}});
db.languages.insert({"name" : "python", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 3, "script" : false, "homepage" : "https://www.python.org/", "download" : "https://www.python.org/downloads/", "votes" : 0}});
db.languages.insert({"name" : "javascript", "codedetail" : { "usecase" : "web, client-side", "rank" : 7, "script" : false, "homepage" : "https://en.wikipedia.org/wiki/JavaScript", "download" : "n/a", "votes" : 0}});
db.languages.insert({"name" : "go", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 12, "compiled" : true, "homepage" : "https://golang.org", "download" : "https://golang.org/dl/", "votes" : 0}});
db.languages.insert({"name" : "java", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 1, "compiled" : true, "homepage" : "https://www.java.com/en/", "download" : "https://www.java.com/en/download/", "votes" : 0}});
db.languages.insert({"name" : "nodejs", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 20, "script" : false, "homepage" : "https://nodejs.org/en/", "download" : "https://nodejs.org/en/download/", "votes" : 0}});

db.languages.find().pretty();

> Create mongo secret:
    kubectl apply -f mongo-secret.yaml

# API Setup

> Create GO API deployment by running the following command:
   kubectl apply -f api-deployment.yaml
> Expose API deployment through service using the following command:
  kubectl expose deploy api \
 --name=api \
 --type=NodePort \
 --port=80 \
 --target-port=8080

> Forwrd port from local machine to pod:
   kubectl forward-port svc/api 8080

> Test and confirm that the API route URL /languages, and /languages/{name} endpoints can be called successfully:
   curl http://localhost:8080/ok
   curl http://localhost:8080/languages
   curl http://localhost:8080/languages/go

# Check the minikube IP and put it in (env/value:) of frontend-deployment.yaml:
   minikube ip

 # Frontend setup

  > Create the Frontend Deployment resource. In the terminal run the following command:
       kubectl apply -f frontend-deployment.yaml

  > Create a new Service resource of LoadBalancer type. In the terminal run the following command:
    kubectl expose deploy frontend \
 --name=frontend \
 --type=NodePort \
 --port=80 \
 --target-port=8080  

  > Forwrd port from local machine to pod:
     kubectl forward-port svc/frontend 8080

  > Test and confirm that the API route:
     curl -I "http://localhost:8080"

  # BROWSE THE VOTING-WEB-APPLICATION IN ANY BROWSER
     http://localhost:8080








