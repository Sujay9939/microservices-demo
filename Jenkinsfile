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

        stage('Deploy to EKS') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'eu-north-1') {
                    sh '''
                    aws eks update-kubeconfig \
                      --region eu-north-1 \
                      --name wonderful-rock-mountain

                    kubectl get nodes

                    kubectl apply -f ./release/kubernetes-manifests.yaml

                    kubectl rollout status deployment/frontend \
                      --timeout=300s

                    kubectl get pods -A
                    kubectl get svc -A
                    '''
                }
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
