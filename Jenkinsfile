pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG   = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Option 1 : ne rien faire (le code est déjà check-out)
                echo 'Code déjà récupéré par Jenkins (from SCM)'

                // Option 2 : si tu veux être explicite :
                // checkout scm
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
    }
}
