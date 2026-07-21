pipeline {

    agent any

    environment {
        FRONTEND = "deva1605/frontend:latest"
        BACKEND  = "deva1605/backend:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Code Quality') {
            steps {
                echo "Running Code Quality Checks..."
                // Example:
                // sh 'npm run lint'
                // sh 'flake8 backend'
            }
        }

        stage('Automated Tests') {
            steps {
                echo "Running Tests..."
                // Example:
                // sh 'npm test'
                // sh 'pytest'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $FRONTEND frontend
                docker build -t $BACKEND backend
                '''
            }
        }

        stage('Docker Login & Push') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $FRONTEND
                    docker push $BACKEND

                    docker logout
                    '''
                }

            }
        }

        stage('Deploy to Development') {
            steps {
                sh '''
                docker compose down || true
                docker compose up -d
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                curl --fail http://localhost:5000/health
                '''
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Deploy to Production?', ok: 'Deploy'
            }
        }

        stage('Deploy to Production') {
            steps {
                sh '''
                docker compose down
                docker compose up -d
                '''
            }
        }

        stage('Production Health Check') {
            steps {
                sh '''
                curl --fail http://localhost:5000/health
                '''
            }
        }
    }

    post {

        success {

            echo "Pipeline Completed Successfully."

            archiveArtifacts artifacts: '**/*', allowEmptyArchive: true
        }

        failure {

            echo "Deployment Failed. Rolling Back..."

            sh '''
            docker compose down || true
            docker compose up -d || true
            '''

            emailext(
                subject: "Jenkins Build Failed - ${env.JOB_NAME}",
                body: """
Job Name : ${env.JOB_NAME}

Build Number : ${env.BUILD_NUMBER}

Build URL :
${env.BUILD_URL}

The deployment has failed and rollback has been executed.
""",
                to: "baskardeva7@gmail.com"
            )
        }

        always {
            cleanWs()
        }
    }
}
