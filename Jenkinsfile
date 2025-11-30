pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
        IMAGENAME = 'sriram040/flaskapp'
        GITREPO = 'https://github.com/Sriram2004yadav/dockerpractice'
        BRANCH = 'main'
        // 🔥 Point kubectl to your HP user's kubeconfig
        KUBECONFIG = 'C:\\Users\\HP\\.kube\\config'
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
            steps {
                script {
                    bat """
                    echo ===== Using kubeconfig =====
                    echo %KUBECONFIG%

                    echo ===== Current context =====
                    kubectl config current-context

                    echo ===== Workspace contents =====
                    dir

                    echo ===== Applying manifests =====
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml

                    echo ===== Pods =====
                    kubectl get pods

                    echo ===== Services =====
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
