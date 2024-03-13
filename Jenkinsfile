pipeline
{
    agent any

    tools
    {
        maven 'Maven_3.9.6'
    }

    environment
    {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages
    {
        stage('Git Checkout')
        {
            steps()
            {
                git branch: 'main', url: 'https://github.com/DevOpsCloudAutomation/JavaMavenApplication_Argo_CD.git'
            }
        }

        stage('Build Project')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }
        
        stage('SonarQube and Sonatype Nexus')
        {
            parallel
            {
                stage('SonarQube Test')
                {
                    steps()
                    {
                        sh 'mvn sonar:sonar'
                    }
                }
                
                stage('Upload Build Artifact to Sonatype Nexus')
                {
                    steps()
                    {
                        sh 'mvn deploy'
                    }
                }
            }
        }
        
        stage('Build Docker Image')
        {
            steps()
            {
                sh 'docker build -t 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber} .'
            }
        }

        stage('Push Docker Image to AWS ECR Registry')
        {
            steps()
            {
                sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 236536187964.dkr.ecr.ap-south-1.amazonaws.com"
                sh "docker push 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber}"
            }
        }

        stage('Remove Docker Image Locally in Jenkins Server')
        {
            steps()
            {
                sh 'docker rmi -f 236536187964.dkr.ecr.ap-south-1.amazonaws.com/webapplication-argocd:${buildNumber}'
            }
        }
        
        stage("Trigger Continuous Delivery Pipeline - Argo CD")
        {
            steps()
            {
                sh "curl -v -k --user DevOpsCloudAutomation:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-15-207-114-187.ap-south-1.compute.amazonaws.com:8080/job/CD_Pipeline/buildWithParameters?token=GitOps_ArgoCD'"
            }
        }
    }
    
    post
    {
        always
        {
            cleanWs()
        }
    }
}