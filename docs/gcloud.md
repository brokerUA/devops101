# Google Cloud Platform and Kubernetes

## Base setup
1. Go to [console.cloud.google.com](https://console.cloud.google.com/) and log in.
    > Consider [setting a cloud budget](https://cloud.google.com/billing/docs/how-to/budgets) for your Google Cloud account in order to make sure you don’t accidentally spend more than you wish to.

2. Go to and enable the [Kubernetes Engine API](https://console.cloud.google.com/apis/api/container.googleapis.com/overview).

3. Use a web based terminal:
    ```bash
    gcloud beta interactive
    ```

4. Create a new project.
    ```bash
    gcloud projects create devops-101-dmytro-a
    gcloud config set project devops-101-dmytro-a
    ```

5. Create a managed Kubernetes cluster and a default node pool.
    ```bash
    gcloud container clusters create \
    --machine-type e2-medium \
    --num-nodes 2 \
    --zone us-central1-c \
    --cluster-version latest \
    --project devops-101-dmytro-a \
    demo
    ```

6. Get credentials for the cluster.
    ```bash
    gcloud container clusters get-credentials demo --zone us-central1-c
    ```
   
7. Check resources and set alias.
    ```bash
    alias k=kubectl
    k get all -A
    ```

## Docker
1. Create Dockerfile
   ```bash
   mkdir demo && cd demo
   echo -e "FROM busybox\nCMD while true; do { echo -e 'HTTP/1.1 200 OK\\\\n\\\\n Version 1.0.0'; } | nc -vlp 8080; done\nEXPOSE 8080" > Dockerfile
   ```
   
2. Build and push the image to Google Container Registry.
   ```bash
   docker build -t gcr.io/devops-101-dmytro-a/demo:1.0.0 .
   docker push gcr.io/devops-101-dmytro-a/demo:1.0.0
   ```
   
3. Config namespace.
   ```bash
   k create ns demo
   k config set-context --current --namespace demo
   ```
   
## Kubernetes
1. Create a deployment.
   ```bash
    k create deploy demo --image gcr.io/devops-101-dmytro-a/demo:1.0.0
    ```
   
2. Check the deployment.
   ```bash
   k get deploy demo
   k get po
   k get all
   ```
   
3. Config LoadBalancer service.
   ```bash
   k expose deploy demo --type LoadBalancer --port 80 --target-port 8080
   ```

4. Check the service.
   ```bash
   k get svc -w
   ```
   
5. Check the application.
   ```bash
   curl <EXTERNAL-IP>
   ```
   
## Create a new version
1. Save IP.
   ```bash
   LB=$(k get svc demo -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   ```

2. Update the image.
   ```bash
   echo -e "FROM busybox\nCMD while true; do { echo -e 'HTTP/1.1 200 OK\\\\n\\\\n Version 2.0.0'; } | nc -vlp 8080; done\nEXPOSE 8080" > Dockerfile
   ```
   
3. Build and push the new image.
   ```bash
   docker build -t gcr.io/devops-101-dmytro-a/demo:2.0.0 .
   docker push gcr.io/devops-101-dmytro-a/demo:2.0.0
   ```
   
4. Zero-downtime deployment.
   ```bash
   k set image deploy demo demo gcr.io/devops-101-dmytro-a/demo:2.0.0
   ```
   
5. Show history.
   ```bash
   k rollout history deploy demo
   ```

6. Rollback.
   ```bash
   k rollout undo deploy demo --to-revision=1
   ```
   
7. Annotated history.
   ```bash
   k annotate deploy demo kubernetes.io/change-cause="updated to 2.0.0"
   ```
   
## Clear resources
1. Delete the deployment.
   ```bash
   k delete deploy demo
   ```
   
2. Delete the service.
   ```bash
    k delete svc demo
    ```
   
3. Delete the namespace.
    ```bash
    k delete ns demo
    ```
   
4. Delete the project.
    ```bash
    gcloud projects delete devops-101-dmytro-a
    ```