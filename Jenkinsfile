pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
        IMAGENAME = 'sriram040/flaskapp'
        GITREPO = 'https://github.com/Sriram2004yadav/dockerpractice'
        BRANCH = 'main'
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

        // OPTIONAL: only if kubectl is installed on Jenkins and configured
        stage('Deploy to Kubernetes') {
            when {
                expression { return false } // change to `true` when ready
            }
            steps {
                script {
                    echo 'Applying Kubernetes manifests...'
                    // Make sure deployment.yaml and service.yaml are in repo root
                    bat "kubectl apply -f deployment.yaml"
                    bat "kubectl apply -f service.yaml"
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
