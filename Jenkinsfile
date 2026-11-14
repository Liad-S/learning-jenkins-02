pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run --rm -v $WORKSPACE:/app -w /app $IMAGE_NAME:$IMAGE_TAG pytest --junitxml=results.xml'
            }
            post {
                always {
                    junit 'results.xml'
                }
            }
        }
                stage('Publish') {
            steps {
                archiveArtifacts artifacts: 'results.xml', fingerprint: true
                sh 'docker tag $IMAGE_NAME:$IMAGE_TAG localhost:5000/$IMAGE_NAME:$IMAGE_TAG'
                sh 'docker push localhost:5000/$IMAGE_NAME:$IMAGE_TAG'
            }
        }
    }
}