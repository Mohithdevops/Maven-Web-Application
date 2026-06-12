pipeline
{
    agent any

    tools
    {
        maven 'Maven_3.9.15'
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
                git branch: 'DevOpsDecemberBatch', url: 'https://github.com/MithunTechnologiesDevOps/Maven-Web-Application.git'
            }
        }

        stage('Build Project')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }

        stage('Create Docker Image')
        {
            steps()
            {
                sh 'docker build -t 149536451818.dkr.ecr.ap-south-1.amazonaws.com/login-service:${buildNumber} .'
            }
        }

        stage('Authenticate and Push Docker Image to AWS ECR')
        {
            steps()
            {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 149536451818.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker push 149536451818.dkr.ecr.ap-south-1.amazonaws.com/login-service:${buildNumber}'
            }
        }
        
        stage('Remove Docker Image Locally from Jenkins')
        {
            steps()
            {
                sh 'docker rmi 149536451818.dkr.ecr.ap-south-1.amazonaws.com/login-service:${buildNumber}'
            }
        }

        stage('Update Image Tag in Kubernetes Manifest')
        {
            steps()
            {
                sh "sed -i 's/Build_Tag/${buildNumber}/g' MavenWebApplication.yaml"
            }
        }

        stage('Deploy Micro Service to EKS Cluster')
        {
            steps()
            {
                sh 'kubectl delete deployment webpage-deployment -n production || true'
                sh 'kubectl apply -f MavenWebApplication.yaml'
            }
        }
    }
}