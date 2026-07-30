pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy to AWS') {
            steps {
                sshagent(['aws-ec2-key']) {
                    sh '''
                    scp target/*.jar ubuntu@ec2-100-54-242-230.compute-1.amazonaws.com:/home/ubuntu/
                    '''
                }
            }
        }
    }
}
