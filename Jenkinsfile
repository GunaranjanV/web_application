pipeline {
    agent any
    
    tools {
        jdk 'java-11'
        maven 'maven'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/GunaranjanV/web_application.git'
            }
        }
        stage('Build Webapp Module') {
            steps {
                // Build only the webapp module, skip tests for speed
                sh 'mvn -pl webapp clean package -DskipTests'
            }
        }
        stage('Docker Image') {
            steps {
                sh 'docker build -t webapp:latest .'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -it -d --name webapp-container -p 9000:8080 webapp:latest'
            }
        }
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                                 credentialsId: 'dockerhub-credentials', 
                                 usernameVariable: 'DOCKERHUB_USERNAME', 
                                 passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'
                    sh 'docker tag webapp:latest $DOCKERHUB_USERNAME/webapp:latest'
                    sh 'docker push $DOCKERHUB_USERNAME/webapp:latest'
                }
            }
        }
    }
}
