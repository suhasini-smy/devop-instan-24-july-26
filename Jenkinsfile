pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir('backend') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Test') {
            steps {
                dir('backend') {
                    sh 'mvn test'
                }
            }
        }

        // stage('Deploy to AWS') {
        //     steps {
        //         withCredentials([
        //             sshUserPrivateKey(
        //                 credentialsId: 'aws-ec2-key',
        //                 keyFileVariable: 'SSH_KEY',
        //                 usernameVariable: 'SSH_USER'
        //             )
        //         ]) {

        //             sh '''
        //             scp -i $SSH_KEY \
        //             -o StrictHostKeyChecking=no \
        //             backend/target/*.jar \
        //             $SSH_USER@ec2-54-224-8-129.compute-1.amazonaws.com:/home/ubuntu/
        //             '''
        //         }
        //     }
        // }

        stage('Deploy to AWS') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'aws-ec2-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'

                )]) {
                    sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no \
                    backend/target/myapp-0.0.1-SNAPSHOT.jar \
                    ubuntu@ec2-54-224-8-129.compute-1.amazonaws.com:/var/www/html/
                    '''
                }
            }
        }


       
    }
}
