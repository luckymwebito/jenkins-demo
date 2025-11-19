pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "luckym003/jenkins-demo"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/luckymwebito/jenkins-demo.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {

                    sh """
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh 'docker rm -f jenkins-demo || true'
                    sh 'docker run -d --name jenkins-demo -p 3000:3000 ${DOCKER_IMAGE}:latest'
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
