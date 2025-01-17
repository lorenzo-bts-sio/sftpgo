pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeLocalServer'  // Nom du serveur SonarQube défini dans Jenkins
        DOCKER_IMAGE = 'my-go-app'                // Nom de l'image Docker
        GO_DOCKER_IMAGE = 'golang:1.20'           // Image officielle Go pour les tests
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Checking out source code"
                }
                checkout scm
            }
        }

        

        stage('SonarQube Analysis') {
            steps {
                script {
                    echo "Running SonarQube analysis"
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh '''
                        docker run --rm -v $(pwd):/app -w /app ${GO_DOCKER_IMAGE} sh -c "
                            sonar-scanner \
                              -Dsonar.projectKey=my-go-project \
                              -Dsonar.sources=. \
                              -Dsonar.exclusions=**/vendor/** \
                              -Dsonar.go.coverage.reportPaths=coverage.out
                        "
                        '''
                    }
                }
            }
        }

        stage('Wait for Quality Gate') {
            steps {
                script {
                    echo "Waiting for SonarQube Quality Gate"
                    timeout(time: 2, unit: 'MINUTES') {
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Quality Gate failed: ${qualityGate.status}"
                        }
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image"
                    sh '''
                    docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} .
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "Cleaning workspace after build"
            }
            deleteDir()
        }

        failure {
            script {
                echo "Pipeline failed!"
            }
        }

        success {
            script {
                echo "Build and tests completed successfully!"
            }
        }
    }
}
