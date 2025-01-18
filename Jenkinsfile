pipeline {
    agent any

    environment {
        // Répertoire de travail dans le conteneur Docker
        WORKSPACE_DIR = '/app'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'golang:1.23'
                }
            }
            steps {
                echo 'Building the Go project...'
                sh '''
                    cd $WORKSPACE_DIR
                    go version
                    go mod tidy
                    go build -o build/app
                '''
            }
        }

        stage('Run Tests') {
            agent {
                docker {
                    image 'golang:1.23'
                }
            }
            steps {
                echo 'Running tests...'
                sh '''
                    cd $WORKSPACE_DIR
                    mkdir -p coverage
                    go test ./... -coverprofile=coverage/coverage.out
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Build and tests completed successfully!'
        }
        failure {
            echo 'Build or tests failed. Please check the logs.'
        }
    }
}
