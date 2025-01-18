pipeline {
    agent any

    environment {
        WORKSPACE_DIR = '/app'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'golang:1.23' // Assurez-vous que cette version existe
                    args '-v $PWD:/app -w /app' // Monte le volume pour accéder au code
                }
            }
            steps {
                echo 'Building the Go project...'
                sh '''
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
                    args '-v $PWD:/app -w /app'
                }
            }
            steps {
                echo 'Running tests...'
                sh '''
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
