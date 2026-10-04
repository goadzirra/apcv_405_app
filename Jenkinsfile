pipeline {
    agent any

    environment {
        IMAGE     = 'ccgoad/apcv_405_app'
        CONTAINER = 'apcv_405_app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                bat 'dotnet test apcv_405_app.sln'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE%:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat 'docker push %IMAGE%:latest'
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    bat(script: 'docker rm -f %CONTAINER%', returnStatus: true)
                }
                bat 'docker run -d --name %CONTAINER% -p 9090:8080 %IMAGE%:latest'
            }
        }

        stage('Health Check') {
            steps {
                sleep(time: 10, unit: 'SECONDS')
                bat 'curl -f http://localhost:9090/health'
            }
        }
    }
}