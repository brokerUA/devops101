# Project: devops101

Dependencies:
- [Docker](https://www.docker.com/)
- [Git](https://git-scm.com/)
- [GoLang](https://golang.org/)
- [K3d](https://k3d.io/)

## GoLang
Build application:
```bash
go build -o bin/app src/main.go
```

Install modules:
```bash
cd src && go mod init devops101
```

## Docker
Build image:
```bash
docker build -t devops101 .
```

Tag image:
```bash
docker tag devops101:latest devops101:1.0.0
```

Login to Docker Hub:
```bash
docker login
```

Push image:
```bash
docker push brokerua/devops101:1.0.0
```

## Kubernetes
Create cluster:
```bash
k3d cluster create devops101
```

Deploy application:
```bash
kubectl create deploy devops101 --image brokerua/devops101:1.0.0
```

Expose application:
```bash
kubectl get po -w
```

Run port-forward:
```bash
kubectl port-forward deploy/devops101 8080:8080
```