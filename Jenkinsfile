pipeline {
    agent any
    triggers {
        githubPush()
    }
    tools {
        maven "Maven-3"
    }
    environment {
        DOCKERHUB_CRED = credentials('dockerhub-id')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rahulsh01/spring-petclinic.git'
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t rahulsh01/petclinic:latest .
                '''
            }
        }
        stage('Docker Login') {
            steps {
                sh '''
                    echo "$DOCKERHUB_CRED_PSW" | docker login -u "$DOCKERHUB_CRED_USR" --password-stdin
                '''
            }
        }
        stage('Push to DockerHub') {
            steps {
                sh '''
                    docker push rahulsh01/petclinic:latest
                '''
            }
        }
        stage('Deploy Locally') {
            steps {
                sh '''
                    docker rm -f petclinic_app || true
                    docker run -d -p 8081:8080 --name petclinic_app rahulsh01/petclinic:latest
                '''
            }
        }
    }
}
