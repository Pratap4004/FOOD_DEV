pipeline {
    agent any

    environment {
        DOCKER_HUB = "prathap4004"
        BACKEND_IMAGE = "food-backend"
        FRONTEND_IMAGE = "food-frontend"
        TAG = "v1"

        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    tools {
        nodejs "nodejs"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Pratap4004/FOOD_DEV.git'
            }
        }

        // =========================
        // FRONTEND BUILD
        // =========================

        stage('Frontend Install') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                }
            }
        }

        stage('Frontend Build') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        // =========================
        // BACKEND BUILD
        // =========================

        stage('Backend Build') {
            steps {
                dir('backend') {

                    // For Maven project
                    sh 'mvn clean package -DskipTests'

                    // If Node backend use below instead
                    // sh 'npm install'
                }
            }
        }

        // =========================
        // DOCKER BUILD
        // =========================

        stage('Docker Build Images') {
            steps {

                dir('frontend') {
                    sh 'docker build -t $DOCKER_HUB/$FRONTEND_IMAGE:$TAG .'
                }

                dir('backend') {
                    sh 'docker build -t $DOCKER_HUB/$BACKEND_IMAGE:$TAG .'
                }
            }
        }

        // =========================
        // DOCKER LOGIN
        // =========================

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "$DOCKER_CREDENTIALS_ID",
                    passwordVariable: 'DOCKER_PASSWORD',
                    usernameVariable: 'DOCKER_USERNAME'
                )]) {

                    sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    '''
                }
            }
        }

        // =========================
        // PUSH IMAGES
        // =========================

        stage('Push Docker Images') {
            steps {

                sh 'docker push $DOCKER_HUB/$FRONTEND_IMAGE:$TAG'
                sh 'docker push $DOCKER_HUB/$BACKEND_IMAGE:$TAG'
            }
        }

        // =========================
        // KUBERNETES DEPLOYMENT
        // =========================

        stage('Deploy To Kubernetes') {
            steps {

                sh 'kubectl apply -f k8s/'

            }
        }
    }

    post {

        success {
            echo 'Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
