pipeline {
    agent any
    parameters {
        booleanParam(name: 'BD_IMG_CHANGED', defaultValue: false, description: 'Set to true if backend image needs to be rebuilt')
        booleanParam(name: 'FD_IMG_CHANGED', defaultValue: false, description: 'Set to true if frontend image needs to be rebuilt')
    }

    environment {
        DOCKER_HUB = "docker.io"
        DOCKER_USERNAME = "Replace_with_yours"
        BACKEND_IMAGE = "${DOCKER_USERNAME}/world-time-app-backend"
        FRONTEND_IMAGE = "${DOCKER_USERNAME}/world-time-app-frontend"
        IMAGE_TAG_BD = "1.0" ##Change version here
        IMAGE_TAG_FD = "1.0"
        PREV_IMAGE_TAG_BD = "1.0"
        PREV_IMAGE_TAG_FD = "1.0"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
    }

    stages {
        stage('Prepare Environment') {
            steps {
                echo "Preparing the environment..."
                sh "docker --version"
            }
        }

        stage('Clone Repository') {
            steps {
                echo "Cloning the GitLab repository..."
                git branch: 'main', url: 'https://github.com/devopsritiks/wta_jenkins_docker.git'
            }
        }

        stage('Build and Push Backend Image') {
            when {
                expression { params.BD_IMG_CHANGED == true }
            }
            steps {
                echo "Building and pushing new backend image..."
                withCredentials([usernamePassword(credentialsId: 'Replace_with_yours', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        docker logout || true
                        echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin
                        docker rmi ${BACKEND_IMAGE}:${PREV_IMAGE_TAG_BD} || true
                        docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG_BD} ./backend
                        docker push ${BACKEND_IMAGE}:${IMAGE_TAG_BD}
                    '''
                }
            }
        }

        stage('Build and Push Frontend Image') {
            when {
                expression { params.FD_IMG_CHANGED == true }
            }
            steps {
                echo "Building and pushing new frontend image..."
                withCredentials([usernamePassword(credentialsId: 'Replace_with_yours', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        docker logout || true
                        echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin
                        docker rmi ${FRONTEND_IMAGE}:${PREV_IMAGE_TAG_FD} || true
                        docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG_FD} ./frontend
                        docker push ${FRONTEND_IMAGE}:${IMAGE_TAG_FD}
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                echo "Deploying the application using docker-compose..."
                withEnv(["COMPOSE_HTTP_TIMEOUT=1800"]) {
                    sh '''
                        docker-compose down || true
                        docker-compose up -d
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check logs for details."
        }
    }
}

