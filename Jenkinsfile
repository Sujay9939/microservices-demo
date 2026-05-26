pipeline {
    agent any

    environment {
        AWS_REGION = "eu-north-1"
        EKS_CLUSTER = "wonderful-rock-mountain"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Sujay9939/microservices-demo.git'
            }
        }

        stage('Connect to EKS') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh """
                    aws eks update-kubeconfig \
                      --region ${AWS_REGION} \
                      --name ${EKS_CLUSTER}

                    kubectl get nodes
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f ./release/kubernetes-manifests.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl rollout status deployment/frontend --timeout=300s

                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully to EKS'
        }

        failure {
            echo 'Deployment failed'
        }

        always {
            cleanWs()
        }
    }
}
