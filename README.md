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

Push image (must be logged in with `docker login`):
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

## Demo
Go to [localhost:8080](http://localhost:8080) to see the application running.

# Development process
1. Create a new branch:
```bash
git checkout -b feature/your-feature
```

2. Make changes to the code.
3. Commit changes:
```bash
git add .
git commit -m "Your message"
```

4. Push changes:
```bash
git push origin feature/your-feature
```

5. Create a pull request on GitHub.
6. Merge the pull request.
7. Delete the branch:
```bash
git checkout main
git branch -d feature/your-feature
```

8. Pull changes:
```bash
git pull
```

9. Build and push the new image with the new version:
```bash
docker build -t brokerua/devops101:1.0.1 .
```

10. Push the new image:
```bash
docker push docker.io/brokerua/devops101:1.0.1
```

11. Get current deployment:
```bash
kubectl get deploy devops101 -o wide
```

12. Set the new image:
```bash
kubectl set image deploy/devops101 devops101=brokerua/devops101:1.0.1
```

13. Check the rollout status:
```bash
kubectl rollout status deploy/devops101
```