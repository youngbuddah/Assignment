pipeline {
    agent any

    environment {

        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')

        DOCKERHUB_USERNAME = "ironfang26"

        BACKEND_IMAGE = "ironfang26/backend:latest"
        FRONTEND_IMAGE = "ironfang26/frontend:latest"

        AWS_DEFAULT_REGION = "us-west-2"
    }

    stages {

        stage('Checkout Code') {
            steps {

                git branch: 'main',
                url: 'https://github.com/youngbuddah/Assignment-01.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {

                dir('backend') {

                    sh '''
                    docker build -t $BACKEND_IMAGE .
                    '''
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {

                dir('frontend') {

                    sh '''
                    docker build -t $FRONTEND_IMAGE .
                    '''
                }
            }
        }

        stage('DockerHub Login') {
            steps {

                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
            }
        }

        stage('Push Backend Image') {
            steps {

                sh '''
                docker push $BACKEND_IMAGE
                '''
            }
        }

        stage('Push Frontend Image') {
            steps {

                sh '''
                docker push $FRONTEND_IMAGE
                '''
            }
        }

        stage('Deploy To EKS Kubernetes Cluster') {
            steps {

                sh '''
                kubectl apply -f backend/k8s/backend-deployment.yaml
                kubectl apply -f backend/k8s/backend-service.yaml

                kubectl apply -f frontend/k8s/frontend-deployment.yaml
                kubectl apply -f frontend/k8s/frontend-service.yaml
                '''
            }
        }

        stage('Verify Kubernetes Deployment') {
            steps {

                sh '''
                kubectl get deployments
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {

        success {

            echo 'CI/CD Pipeline Executed Successfully'
        }

        failure {

            echo 'CI/CD Pipeline Failed'
        }
    }
}
