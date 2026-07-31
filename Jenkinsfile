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


       stage('Deploy to AWS') {
                steps {
                    withCredentials([sshUserPrivateKey(
                        credentialsId: 'aws-ec2-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )]) {
                        sh '''
                        # Deploy Spring Boot JAR
                        scp -i $SSH_KEY -o StrictHostKeyChecking=no \
                        backend/target/myapp-0.0.1-SNAPSHOT.jar \
                        ubuntu@ec2-54-224-8-129.compute-1.amazonaws.com:/var/www/html/

                        # Deploy frontend HTML
                        scp -i $SSH_KEY -o StrictHostKeyChecking=no \
                        index.html \
                        ubuntu@ec2-3-91-246-209.compute-1.amazonaws.com:/tmp/

                        # Move HTML file to web root
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no \
                        ubuntu@ec2-3-91-246-209.compute-1.amazonaws.com "
                        sudo mv /tmp/index.html /var/www/html/index.html
                        sudo chown www-data:www-data /var/www/html/index.html
                        "
                        '''
                    }
                }
            }


       
    }
}
