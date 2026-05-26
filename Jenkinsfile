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

        stage('Deploy to Kubernetes') {
    steps {
        withAWS(credentials: 'aws-creds', region: 'eu-north-1') {
            sh '''
            kubectl apply -f ./release/kubernetes-manifests.yaml
            '''
        }
    }
}

stage('Verify Deployment') {
    steps {
        withAWS(credentials: 'aws-creds', region: 'eu-north-1') {
            sh '''
            kubectl get pods -A
            kubectl get svc -A

            kubectl rollout status deployment/frontend \
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
