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

## How to setup and run
### prerequires

- Minikube
- kubectl
- Docker 
- Helm 
- Git

run the command to check if you have everything needed:

git --version && docker version && minikube version && kubectl version && helm version


### How to setup and run:




### TODO



