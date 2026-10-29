# Jenkins + Docker + Kubernetes starter (Windows / Docker Desktop)

This is a small Java 17 / Spring Boot example repository. It is a reusable *structure*, not a universal set of files for every programming language. Keep the Jenkins/Kubernetes workflow and replace the app source, build command, and Dockerfile to match your application's runtime.

## Files
- `Jenkinsfile` — checkout, Maven build, Docker build/push, Kubernetes connection check, deployment.
- `pom.xml` — required for this Java/Maven example only. Not needed for Python/Node.js apps.
- `Dockerfile` — packages the built Java JAR.
- `k8s/deployment.yaml` — Kubernetes Deployment.
- `k8s/service.yaml` — NodePort Service at port 30080.
- `src/main/java/com/example/demo/DemoApplication.java` — sample web app with `/` and `/health` endpoints.

## Before using
1. Change `harshi1802/k8s-demo-app` in both `Jenkinsfile` and `k8s/deployment.yaml` to your own Docker Hub repository.
2. In Jenkins, create username/password credentials with ID `dockerhub-cred` for Docker Hub.
3. In Jenkins, add a **Secret file** credential with ID `kubeconfig-file`, containing a working kubeconfig for the Docker Desktop cluster.
4. Ensure Jenkins runs on the same Windows host as Docker Desktop Kubernetes and can access the API server address in that kubeconfig. If Jenkins runs as a Windows service under a different account, check file permissions and connectivity.
5. Ensure `mvn`, `docker`, and `kubectl` are available to the Jenkins process. This Jenkinsfile includes the kubectl path observed in one Windows setup; edit it if your path differs.
6. Ensure the Jenkins account is authorized to deploy to the cluster. Do not commit kubeconfig, tokens, passwords, or certificates into Git.
7. Create a Git repository, add these files, and push them.
8. In Jenkins, create a Pipeline job, choose **Pipeline script from SCM**, select **Git**, enter the repository URL and branch, and set Script Path to `Jenkinsfile`.

## Run locally
From the repository root, run:

```cmd
mvn -B clean package -DskipTests
docker build -t YOUR_DOCKERHUB_USER/k8s-demo-app:latest .
```

The Jenkins pipeline pushes a build-number tag and `latest`, then deploys the build-number tag. The service uses NodePort `30080`; with Docker Desktop Kubernetes, try `http://localhost:30080` after deployment. If that port is not reachable in your environment, use `kubectl port-forward service/k8s-demo-app-service 8081:8080` and open `http://localhost:8081`.

## If the question only asks to configure and verify Jenkins–Kubernetes connectivity
Use only the `Verify Kubernetes Connection` stage. Docker Hub credentials, Maven build, and application deployment are not necessary for connection verification alone.
