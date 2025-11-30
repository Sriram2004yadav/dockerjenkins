pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
        IMAGENAME = 'sriram040/flaskapp'
        GITREPO = 'https://github.com/Sriram2004yadav/dockerpractice'
        BRANCH = 'main'
        // Set this if you use a custom kubeconfig location (optional)
        // KUBECONFIG = 'C:\\path\\to\\kubeconfig'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                git branch: "${BRANCH}", url: "${GITREPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    // Tag with build number AND latest
                    bat "docker build -t ${IMAGENAME}:${BUILD_NUMBER} -t ${IMAGENAME}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Logging into Docker Hub & pushing images...'
                    bat "docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}"
                    bat "docker push ${IMAGENAME}:${BUILD_NUMBER}"
                    bat "docker push ${IMAGENAME}:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            // 🔥 remove the "false" condition so it actually runs
            when {
                expression { return true }  // or remove 'when' block entirely
            }
            steps {
                script {
                    echo 'Deploying to Kubernetes...'
                    // Make sure kubectl is installed and in PATH on this Jenkins node
                    bat """
                    kubectl version --client
                    kubectl config current-context
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl get pods
                    kubectl get svc
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
