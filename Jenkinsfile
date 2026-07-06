pipeline {
    agent {
        label 'agent-1'
    }

    environment {
        APP_DIR = "app"
        IMAGE_NAME = "saishanker/java-demo"
        NAMESPACE = ""
    }

    stages {

        stage('Set Environment') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev') {
                        env.NAMESPACE = 'dev'
                    } else if (env.BRANCH_NAME == 'qa') {
                        env.NAMESPACE = 'qa'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.NAMESPACE = 'prod'
                    }

                    echo "Deploying to namespace: ${env.NAMESPACE}"
                }
            }
        }

        stage('Checkout') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                echo "Building Java application..."
                dir("${APP_DIR}") {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker image..."
                sh """
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                """
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'Docker-hub-creds',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh """
                        echo \$PASS | docker login -u \$USER --password-stdin
                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    sed -i 's|IMAGE_PLACEHOLDER|${IMAGE_NAME}:${BUILD_NUMBER}|g' k8s/deployment.yaml
                    kubectl apply -f k8s/deployment.yaml -n ${NAMESPACE}
                    kubectl apply -f k8s/service.yaml -n ${NAMESPACE}
                    kubectl rollout status deployment/demo-app -n ${NAMESPACE}
                """
            }
        }
    }
}

