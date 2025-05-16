pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Clean Up Docker') {
            steps {
                script {
                    sh "echo Cleaning up existing containers and images..."
                    sh "docker stop nodejs-project || true"
                    sh "docker rm nodejs-project || true"
                    sh "docker rmi nodejs-project:${BUILD_NUMBER} || true"
                    sh "docker rmi nodejs-project:latest || true"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t nodejs-project:${BUILD_NUMBER} ."
                    sh "docker tag nodejs-project:${BUILD_NUMBER} nodejs-project:latest"
                }
            }
        }

        stage('Manual Approval') {
            steps {
                script {
                    input message: 'Proceed with deployment?', ok: 'Deploy'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker run -d --name nodejs-project -p 5000:80 nodejs-project:${BUILD_NUMBER}"
                    sh "docker ps -a"
                }
            }
        }
    }

    post {
        always {
            script {
                sh "echo Pipeline execution completed."
            }
        }
        success {
            script {
                sh "echo Deployment successful."
            }
        }
        failure {
            script {
                sh "echo Build or deployment failed. Check logs above."
            }
        }
    }
}
