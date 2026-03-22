# A Docker & Kubernetes Demo

## A minimal application containerized with Docker and deployed on Kubernetes using Minikube.


## Architecture

OSI-Layer interaction:
* L7 (Application)   HTTP (Express/Node.js)
* L6 (Presentation)  TLS not used. HTTP only. Traffic is internal to the cluster
* L5 (Session)       Not used. No persistent session handling
* L4 (Transport)     TCP (port 3000 internal, port 80 exposed via NodePort 31758)
* L3 (Network)       Pod IPs (10.x.x.x via CNI), tunnelled via minikube to 127.0.0.1
* L2 (Data Link)     Virtual network (minikube docker bridge)
* L1 (Physical)      Local machine 


**This setup is intended for a local development environment.**

It is designed to:
- Run locally using Minikube
- Follow basic docker and Kubernetes best practices
  
A representation (made in Draw.io) of how minikube works:

<img width="467" height="413" alt="image" src="https://github.com/user-attachments/assets/dd78b680-c1b6-4260-b525-bbafa30e6144" />

"Inside the Matrix" refers to: Inside the container.



## How to setup and run
### prerequires

- [Git](https://git-scm.com/downloads)
- [Docker](https://docs.docker.com/get-docker/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)

run the command to check if you have everything needed:

git --version && docker version && minikube version && kubectl version && helm version


### How to setup and run

#### 1. Clone the repository
```bash
git clone https://github.com/danieljari/node-hostname-test
cd node-hostname-test
```

#### 2. Start Minikube
```bash
minikube start
```

#### 3. Login and push Docker image
You need a [Docker Hub](https://hub.docker.com) account.
> Replace `<your-dockerhub-username>` with your Docker Hub username.
```
eval $(minikube docker-env -u)
docker login
docker build -t <your-dockerhub-username>/node-hostname:latest .
docker push <your-dockerhub-username>/node-hostname:latest
```

#### 4. Deploy with Helm
```
helm install node-hostname ./node-hostname-chart
```

#### 5. Access the application
```
minikube service node-hostname-service
```
Press `Ctrl + C` to stop the tunnel when done.

#### 6. Rolling update
Edit `routes/index.js` and change the release field from v2 to v3:
```
router.get('/', function(req, res, next) {
  res.send({
    hostname: os.hostname(),
    version: process.env.npm_package_version,
    release: 'v3',
  });
});
```

Then build, push and update:
``` 
docker build -t <your-dockerhub-username>/node-hostname:v3 .
docker push <your-dockerhub-username>/node-hostname:v3
kubectl set image deployments/node-hostname node-hostname=<your-dockerhub-username>/node-hostname:v3
kubectl rollout status deployments/node-hostname
```

#### 7. Verify Update works
Verify it says: v3 in the web-app
```
minikube service node-hostname-service
```


#### 8. Rollback if needed
```
kubectl rollout undo deployments/node-hostname
```

press ctrl + C to to stop the tunnel when done.

#### 9. Verify rollback works
It should NO LONGER say v3 :)
```
minikube service node-hostname-service
```
Press `Ctrl + C` to stop the tunnel when done.

#### 10. Good job!
  Well done!



## TODO

Slow at peak hours:

- [ ] Add resource requests and limits
- [ ] Implement horizontal pod autoscaling (HPA)
- [ ] Implement high availability (multi-zone)

Downtime from power cut:

- [ ] Move from local Minikube to a cloud provider (GCP/AWS/Azure)
- [ ] Implement high availability (multi-zone)
- [ ] Add readiness and liveness probes

Slow/stressful deployments:

- [ ] Build automated CI/CD pipelines
- [ ] Separate development and production environments
- [ ] Add automated tests

Security & production hardening:

- [ ] Configure securityContext (non-root, least privilege)
- [ ] Implement RBAC for access control
- [ ] Enable TLS (HTTPS) 
- [ ] Store TLS certificates in Kubernetes Secrets
- [ ] Implement Secrets for sensitive data
- [ ] Implement ConfigMaps for configuration

Observability:

- [ ] Add Prometheus monitoring
- [ ] Add logging and alerting



## Reflection


A to-do is not easy to do (Ba-dum tss!). 

There is much to consider before production, like architecture, potential selling points and risks and management. A shift in how we can go to production. If we need to store data in a database and use a cloud provider while doing could be a risky business. We have to do things right by preventing the obvious risks associated with everything around production over the internet. From knowing the customer needs, having clear business goals and choosing a provider, deciding database, what data we need to collect, how we protect personal data, how and where we store data, how we back up the data, how we secure our entire envioronment, what tools and technologies we need to use and how we scale up the product for the future for Kent AB. 

I felt incredibly rewarded by going through this process with you. I’ve learned so much through the preparation and research, it was challenging, fun, and deeply satisfying. I gained a better understanding of cloud sovereignty, which is something I’m truly passionate about and believe is an essential right for Europeans when engaging with cloud technologies. I also realized just how vast the world of Kubernetes is. The more I learn, the more I understand how much there still is to explore.

Thank you!



## Sources
### Link to my Github:
https://github.com/danieljari

### Sources I've prepared with, for documentation and learning:
- https://kubernetes.io/docs/home
- https://github.com/danieljari/compliantkubernetes-apps
- https://github.com/elastisys/security-review/blob/0d9632778ff7ffb9b5a2af7eafc26401ecfe7459/kubernetes-information-security-review-checklist.md#governance-risk-management-and-compliance
  
### Some of the practice repostiories I've engaged with for the purpose of learning by doing:
- https://github.com/danieljari/k8s-demo
- https://github.com/danieljari/solve-k8s-problems
- https://github.com/danieljari/microservices-demo
- https://github.com/danieljari/microservices-Springboot


