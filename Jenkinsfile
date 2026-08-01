pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        EC2_HOST = "ec2-3-91-246-209.compute-1.amazonaws.com"
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
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'aws-ec2-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {

                    sh '''
                    set -eux

                    echo "===== Jenkins Workspace ====="
                    pwd
                    ls -l
                    ls -l index.html

                    echo "===== Installing Apache if required ====="

                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no ubuntu@$EC2_HOST <<'EOF'

                    set -eux

                    if ! command -v apache2 >/dev/null 2>&1; then
                        sudo apt-get update
                        sudo apt-get install -y apache2
                    fi

                    sudo mkdir -p /var/run/apache2
                    sudo mkdir -p /var/lock/apache2
                    sudo mkdir -p /var/log/apache2

                    sudo systemctl enable apache2
                    sudo systemctl start apache2

                    EOF

                    echo "===== Copying Website ====="

                    scp -i "$SSH_KEY" \
                        -o StrictHostKeyChecking=no \
                        index.html \
                        ubuntu@$EC2_HOST:/tmp/index.html

                    echo "===== Deploying Website ====="

                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no ubuntu@$EC2_HOST <<'EOF'

                    set -eux

                    sudo cp /tmp/index.html /var/www/html/index.html

                    sudo chmod 644 /var/www/html/index.html

                    sudo systemctl restart apache2

                    echo "Deployment completed"

                    ls -l /var/www/html/index.html

                    EOF
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
