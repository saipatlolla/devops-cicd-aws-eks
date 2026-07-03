pipeline
    agent {
        label 'agent-1'
    }

    environment {
        APP_DIR = "app"
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
    }

    post {
        success {
            echo "CI Build SUCCESS"
        }

        failure {
            echo "CI Build FAILED"
        }
    }
}
