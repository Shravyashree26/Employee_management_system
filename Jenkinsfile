pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKERHUB_USERNAME = 'hemanthpoojary'
    }

    stages {

        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('ems-backend') {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('ems-frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                dir('ems-backend') {
                    sh 'docker build -t hemanthpoojary/employee-backend:v1 .'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('ems-frontend') {
                    sh 'docker build -t hemanthpoojary/employee-frontend:v1 .'
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS')]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                    docker push hemanthpoojary/employee-backend:v1
                    docker push hemanthpoojary/employee-frontend:v1

                    docker logout
                    '''
                }
            }
        }
    }
}