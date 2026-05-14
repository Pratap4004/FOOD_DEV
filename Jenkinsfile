pipeline {

    agent any

    environment {

        SCANNER_HOME = tool 'sonar'

        BACKEND_IMAGE = 'prathap4004/food-backend:v1'
        FRONTEND_IMAGE = 'prathap4004/food-frontend:v1'

    }

    stages {

        stage('Clone Code') {
            steps {

                git(
                    branch: 'main',
                    url: 'https://github.com/Pratap4004/FOOD_DEV.git',
                    credentialsId: 'git-cred'
                )

            }
        }

        stage('Backend Install') {
            steps {

                dir('backend') {

                    sh 'npm install'

                }

            }
        }

        stage('Backend Build') {
            steps {

                dir('backend') {

                    sh 'npm run build || true'

                }

            }
        }

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

        stage('SonarQube Analysis') {
            steps {

                withSonarQubeEnv('sonar') {

                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=food-dev \
                    -Dsonar.projectName=food-dev \
                    -Dsonar.sources=.
                    '''

                }

            }
        }

        stage('Trivy File Scan') {
            steps {

                sh 'trivy fs .'

            }
        }

        stage('Backend Docker Build') {
            steps {

                dir('backend') {

                    sh 'docker build -t $BACKEND_IMAGE .'

                }

            }
        }

        stage('Frontend Docker Build') {
            steps {

                dir('frontend') {

                    sh 'docker build -t $FRONTEND_IMAGE .'

                }

            }
        }

        stage('Docker Push') {
            steps {

                withDockerRegistry(
                    credentialsId: 'docker-cred',
                    url: 'https://index.docker.io/v1/'
                ) {

                    sh 'docker push $BACKEND_IMAGE'
                    sh 'docker push $FRONTEND_IMAGE'

                }

            }
        }

        stage('Deploy To Kubernetes') {
            steps {

                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'

            }
        }

    }

}
