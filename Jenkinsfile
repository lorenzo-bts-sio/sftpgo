pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeLocalServer'  // Nom du serveur SonarQube défini dans Jenkins
        DOCKER_IMAGE = 'my-go-app'                // Nom de l'image Docker
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    echo "Running unit tests"
                    sh '''
                    go mod tidy
                    go test -v ./... -coverprofile=coverage.out
                    '''
                }
            }
        }

        stage('Integration Tests') {
            steps {
                script {
                    echo "Running integration tests"
                    sh '''
                    # Supposons que vos tests d'intégration soient dans un répertoire `integration`
                    go test -v ./integration
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    echo "Running SonarQube analysis"
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=my-go-project \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=**/vendor/** \
                          -Dsonar.go.coverage.reportPaths=coverage.out
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

      

    post {
        always {
            echo "Cleaning workspace after build"
            deleteDir()
        }

       

        success {
            echo "Build completed successfully!"
        }
    }
}
