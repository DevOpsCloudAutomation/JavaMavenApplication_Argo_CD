
# End to End CI Pipeline Project for GitOps ArgoCD

## This Project can be used to Build an End to End CI Pipeline for GitOps ArgoCD

## CI Pipeline Stages

- Checkout Code from GitHub.
- Build Project.
- Execute SonarQube Test.
- Upload Build Artifact to Sonatype Nexus.
- Build Docker Image.
- Push Docker Image to AWS ECR Registry.
- Remove Docker Image Locally in Jenkins.
- Trigger Continuous Delivery Pipeline - ArgoCD.

### Tools and Technologies used are Java, Git, GitHub, Maven, SonarQube, Jenkins, Docker, AWS ECR Registry and Kubernetes.

![CICD](https://github.com/DevOpsCloudAutomation/TestRepository/assets/123757746/060971a4-f730-4fbf-8657-4682af93c896)
  
# Project Execution
## Git Checkout
```bash
  git branch: 'main', url: 'https://github.com/DevOpsCloudAutomation/JavaMavenApplication_Argo_CD.git'
```

## Build Project

Build Automation Tool Maven can be used to build this project as this project is developed using Java Programming Language.

```bash
  mvn clean package
```
Note:  
Java and Maven should be installed as a prerequisite to Build Project Code.

## Execute SonarQube Test
```bash
  mvn sonar:sonar
```

## Upload Build Artifact to Sonatype Nexus
```bash
  mvn deploy
```

## Build Docker Image
```bash
  docker build -t 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber} .
```

## Push Docker Image to AWS ECR Registry
```bash
  aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 236536187964.dkr.ecr.ap-south-1.amazonaws.com
  docker push 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber}
```

## Remove Docker Image Locally in Jenkins Server
```bash
  docker rmi -f 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber}
```

## Trigger Continuous Delivery Pipeline - ArgoCD
```bash
  curl -v -k --user DevOpsCloudAutomation:${Jenkins_API_Token_ArgoCD} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'buildNumber=${buildNumber}' 'ec2-15-207-114-187.ap-south-1.compute.amazonaws.com:8080/job/CD_Pipeline/buildWithParameters?token=GitOps_ArgoCD'
```