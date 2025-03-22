pipeline {
    agent any

    parameters {
        booleanParam(name: 'BD_IMG_CHANGED', defaultValue: false, description: 'Set to true if backend image needs to be rebuilt')
        booleanParam(name: 'FD_IMG_CHANGED', defaultValue: false, description: 'Set to true if frontend image needs to be rebuilt')
    }

    environment {
        //Docker Hub credentials and repository details
        DOCKER_HUB = "docker.io"
        DOCKER_USERNAME = "Replace_with_yours"
        BACKEND_IMAGE = "${DOCKER_USERNAME}/world-time-app-backend"
        FRONTEND_IMAGE = "${DOCKER_USERNAME}/world-time-app-frontend"

        //Generate image versions dynamically based on timestamp to avoid manual updates
        IMAGE_TAG_BD = "1.0-${BUILD_NUMBER}"
        IMAGE_TAG_FD = "1.0-${BUILD_NUMBER}"

        //Credentials ID for Docker login (Best practice: Use Access Token instead of password)
        DOCKER_CREDENTIALS_ID = "docker-hub-access-token"

        //Git repository details
        GIT_REPO = "https://github.com/devopsritiks/wta_jenkins_docker.git"
        GIT_BRANCH = "main"
    }

    options {
        timeout(time: 30, unit: 'MINUTES') //Timeout to prevent stuck builds
    }

    stages {
        stage('Prepare Environment') {
            steps {
                echo "//Checking Docker version..."
                sh "docker --version"
            }
        }

        stage('Clone Repository') {
            steps {
                echo "//Cloning the Git repository..."
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build and Push Backend Image') {
            when {
                expression { params.BD_IMG_CHANGED == true }
            }
            steps {
                echo "//Building and pushing new backend image..."
                withCredentials([string(credentialsId: "${DOCKER_CREDENTIALS_ID}", variable: 'DOCKER_ACCESS_TOKEN')]) {
                    sh '''
                        //Login to Docker using access token (Best Practice)
                        docker logout || true
                        echo $DOCKER_ACCESS_TOKEN | docker login --username $DOCKER_USERNAME --password-stdin

                        //Remove previous image to prevent conflicts
                        docker rmi ${BACKEND_IMAGE}:latest || true

                        //Build and push new image
                        docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG_BD} ./backend
                        docker tag ${BACKEND_IMAGE}:${IMAGE_TAG_BD} ${BACKEND_IMAGE}:latest
                        docker push ${BACKEND_IMAGE}:${IMAGE_TAG_BD}
                        docker push ${BACKEND_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Build and Push Frontend Image') {
            when {
                expression { params.FD_IMG_CHANGED == true }
            }
            steps {
                echo "//Building and pushing new frontend image..."
                withCredentials([string(credentialsId: "${DOCKER_CREDENTIALS_ID}", variable: 'DOCKER_ACCESS_TOKEN')]) {
                    sh '''
                        //Login to Docker using access token (Best Practice)
                        docker logout || true
                        echo $DOCKER_ACCESS_TOKEN | docker login --username $DOCKER_USERNAME --password-stdin

                        //Remove previous image to prevent conflicts
                        docker rmi ${FRONTEND_IMAGE}:latest || true

                        //Build and push new image
                        docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG_FD} ./frontend
                        docker tag ${FRONTEND_IMAGE}:${IMAGE_TAG_FD} ${FRONTEND_IMAGE}:latest
                        docker push ${FRONTEND_IMAGE}:${IMAGE_TAG_FD}
                        docker push ${FRONTEND_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                echo "//Deploying the application using Docker Compose..."
                withEnv(["COMPOSE_HTTP_TIMEOUT=1800"]) {
                    sh '''
                        docker-compose down || true
                        docker-compose up -d
                    '''
                }
            }
        }

        stage('Prune Unused Docker Images') {
            steps {
                echo "//Removing unused Docker images to free up space..."
                sh "docker image prune -af"
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
