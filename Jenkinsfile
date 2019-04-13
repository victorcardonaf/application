pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "victorcardonaf/test-application"
    }
    stages {
        stage('Clone sources'){
            steps {
                git url: 'https://github.com/victorcardonaf/application.git'
                 }
            }
        stage('Build Docker Image') {
            steps {
                script {
                    checkout scm
                    app = docker.build(DOCKER_IMAGE_NAME)
                    }
                }
            }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                  }
              }
            }
        stage('Deploy') {
            steps {
                    sh "docker run --stop-timeout 80 --stop-signal 80 -p 3000:3000 --name test-${env.BUILD_NUMBER} victorcardonaf/test-application"
                }
                
            }
        stage('Cleanup') {
            steps {
                    sh "docker stop test-${env.BUILD_NUMBER}"
                    sh "docker rm test-${env.BUILD_NUMBER}"

                }
                
            }
        }
    }

