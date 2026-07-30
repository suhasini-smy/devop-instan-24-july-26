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
                sshagent(credentials: ['aws-ec2-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no target/*.jar \
                    ubuntu@ec2-100-54-242-230.compute-1.amazonaws.com:/home/ubuntu/

                    ssh -o StrictHostKeyChecking=no \
                    ubuntu@ec2-100-54-242-230.compute-1.amazonaws.com "
                    pkill -f '.jar' || true
                    nohup java -jar /home/ubuntu/*.jar > app.log 2>&1 &
                    "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
