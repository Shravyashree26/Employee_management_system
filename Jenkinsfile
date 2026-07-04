pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKERHUB_USERNAME = 'shravyagowda'
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
                    sh 'docker build -t shravyagowda/employee-backend:v1 .'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('ems-frontend') {
                    sh 'docker build -t shravyagowda/employee-frontend:v1 .'
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                    docker push shravyagowda/employee-backend:v1
                    docker push shravyagowda/employee-frontend:v1

                    docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('/var/lib/jenkins/workspace/employee-management@2') {
                    sh '''
                    docker compose down || true
                    docker compose up -d
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }

        success {
            echo 'Application built, Docker images pushed, and deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output for details.'
        }
    }
}
