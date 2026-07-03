pipeline {
    agent {
        label 'agent-1'
    }

    environment {
        APP_DIR = "app"
        IMAGE_NAME = "saishanker/java-demo"
    }

    stages {

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
        stage ('Push image to dcoker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Docker-hub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    """
                }
            }
        }

    }

}

