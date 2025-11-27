pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG   = "${BUILD_NUMBER}"
        SCANNER_HOME = tool 'SonarQube'  // Nom du scanner dans "Global Tool Configuration"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Code déjà récupéré par Jenkins (from SCM)'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {  // Nom du serveur dans "Configure System"
                    sh "${SCANNER_HOME}/bin/sonar-scanner"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                  docker stop ${DOCKER_IMAGE} || true
                  docker rm ${DOCKER_IMAGE} || true
                  docker run -d --name ${DOCKER_IMAGE} -p 3000:3000 ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
    }

    post {
        success {
            sh 'docker image prune -f'
        }
        failure {
            echo 'Le pipeline a échoué, vérifier les logs.'
        }
    }
}
