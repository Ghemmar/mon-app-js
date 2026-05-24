pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'immaa11/mon-app-js'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Tests') {
            steps {
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Ancien déploiement Docker (remplacé par Kubernetes)
                    // bat 'docker stop mon-app-js & exit 0'
                    // bat 'docker rm mon-app-js & exit 0'
                    // bat "docker run -d --name mon-app-js -p 3000:3000 ${DOCKER_IMAGE}:${DOCKER_TAG}"

                    // Déploiement Kubernetes
                    bat 'kubectl apply -f deployment.yaml'
                    bat 'kubectl apply -f service.yaml'
                }
            }
        }
    }

    post {
        success {
            emailext(
                subject: "SUCCESS - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build ${env.BUILD_NUMBER} a réussi ! \n\nVoir les détails : ${env.BUILD_URL}",
                to: 'amaghem10@gmail.com'
            )
        }
        failure {
            emailext(
                subject: "FAILURE - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build ${env.BUILD_NUMBER} a échoué. \n\nVoir les détails : ${env.BUILD_URL}",
                to: 'amaghem10@gmail.com'
            )
        }
    }
}