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
               withAWS(credentials: 'aws-creds', region: 'eu-north-1') {

                   sh '''
                   aws sts get-caller-identity

                   aws eks update-kubeconfig \
                     --region eu-north-1 \
                     --name wonderful-rock-mountain

                  kubectl get nodes
                  '''
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
                    kubectl get pods -A
                    kubectl get svc -A

                    kubectl rollout status deployment/frontend \
                    -n default \
                    --timeout=300s
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
