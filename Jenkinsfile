pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "victorcardonaf/test-application"
        registryCredential = 'dockerhub'
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
                    app = docker.build(DOCKER_IMAGE_NAME)
                    }
                }
            }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'registryCredential') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                  }
              }
            }
        stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app.run("-d -p 3000:3000 --name image_build_${env.BUILD_NUMBER} -i -t")
                }
                
            }
        }
    }

}
