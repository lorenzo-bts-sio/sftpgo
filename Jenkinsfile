pipeline {
    agent any

    environment {
        // Variables pour SonarQube
        SONARQUBE_SERVER = 'SonarQube' // Nom du serveur SonarQube configuré dans Jenkins
        SONARQUBE_TOKEN_ID = 'sonar-token' // ID du token d'authentification SonarQube dans Jenkins
        SONAR_PROJECT_KEY = 'go-project' // Clé unique du projet dans SonarQube
        WORKSPACE_DIR = '/app' // Répertoire de travail du projet
    }

    stages {
       
        stage('SonarQube Analysis') {
            environment {
                SONARQUBE_TOKEN = credentials('sonar-token')
            }
            steps {
                echo 'Running SonarQube analysis...'
                sh '''
                    docker run --rm \
                    -e SONARQUBE_TOKEN=${SONARQUBE_TOKEN} \
                    -v $PWD:/app \
                    -w /app \
                    sonarsource/sonar-scanner-cli:latest \
                    sonar-scanner \
                    -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                    -Dsonar.sources=. \
                    -Dsonar.tests=. \
                    -Dsonar.test.inclusions="**/*_test.go" \
                    -Dsonar.go.coverage.reportPaths=coverage/coverage.out \
                    -Dsonar.host.url=http://your-sonarqube-server-url \
                    -Dsonar.login=${SONARQUBE_TOKEN}
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Build, tests, and SonarQube analysis completed successfully!'
        }
        failure {
            echo 'Build, tests, or SonarQube analysis failed. Please check the logs.'
        }
    }
}
