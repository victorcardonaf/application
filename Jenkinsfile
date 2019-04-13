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
                    sh "docker run -d -p 300${env.BUILD_NUMBER}:3000 --name test-${env.BUILD_NUMBER} victorcardonaf/test-application"
                }
                
            }
        stage('Test') {
            steps {
                    echo "open http://jenkins-ip:300${env.BUILD_NUMBER}"

                }
                
            }
        }
    }

