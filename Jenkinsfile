pipeline {

    agent any

    environment {
        FRONTEND = "deva1605/frontend"
        BACKEND  = "deva1605/backend"
    }

    stages {

        stage('Code Quality') {
            steps {
                echo "Running code quality..."
            }
        }

        stage('Test') {
            steps {
                echo "Running Tests..."
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t $FRONTEND frontend'
                sh 'docker build -t $BACKEND backend'
            }
        }

        stage('Push Docker') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }

                sh 'docker push $FRONTEND'
                sh 'docker push $BACKEND'
            }
        }

        stage('Deploy Dev') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl http://localhost:5000/health'
            }
        }

        stage('Production Approval') {
            steps {
                input "Deploy Production?"
            }
        }

        stage('Production Deploy') {
            steps {
                echo "Deploying Production"
            }
        }
    }

    post {

        success {
            echo "Pipeline Success"
            archiveArtifacts artifacts: '**/*', allowEmptyArchive: true
        }

        failure {
        emailext(
            subject: "Jenkins Build Failed: ${env.JOB_NAME}",
            body: "Build #${env.BUILD_NUMBER} has failed.\nCheck: ${env.BUILD_URL}",
            to: "baskardeva7@gmail.com"
        )
    }
}
    }
}
