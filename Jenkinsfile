pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        EC2_HOST = "ec2-54-89-84-22.compute-1.amazonaws.com"
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
        //             set -eux

        //             echo "===== Jenkins Workspace ====="
        //             pwd
        //             ls -l
        //             ls -l index.html

        //             echo "===== Installing Apache if required ====="

        //             ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no ubuntu@$EC2_HOST <<'EOF'

        //             set -eux

        //             if ! command -v apache2 >/dev/null 2>&1; then
        //                 sudo apt-get update
        //                 sudo apt-get install -y apache2
        //             fi

        //             sudo mkdir -p /var/run/apache2
        //             sudo mkdir -p /var/lock/apache2
        //             sudo mkdir -p /var/log/apache2

        //             sudo systemctl enable apache2
        //             sudo systemctl start apache2

        //             EOF

        //             echo "===== Copying Website ====="

        //             scp -i "$SSH_KEY" \
        //                 -o StrictHostKeyChecking=no \
        //                 index.html \
        //                 ubuntu@$EC2_HOST:/tmp/index.html

        //             echo "===== Deploying Website ====="

        //             ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no ubuntu@$EC2_HOST <<'EOF'

        //             set -eux

        //             sudo cp /tmp/index.html /var/www/html/index.html

        //             sudo chmod 644 /var/www/html/index.html

        //             sudo systemctl restart apache2

        //             echo "Deployment completed"

        //             ls -l /var/www/html/index.html

        //             EOF
        //             '''
        //         }
        //     }
        // }



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
                                ls -la

                                test -f index.html

                                echo "===== Install Apache ====="

                                ssh -i "$SSH_KEY" \
                                    -o StrictHostKeyChecking=no \
                                    ubuntu@$EC2_HOST '
                                        set -eux

                                        if ! command -v apache2 >/dev/null 2>&1; then
                                            sudo apt-get update
                                            sudo apt-get install -y apache2
                                        fi

                                        sudo systemctl enable apache2
                                        sudo systemctl start apache2
                                    '

                                echo "===== Copy Website ====="

                                scp -i "$SSH_KEY" \
                                    -o StrictHostKeyChecking=no \
                                    index.html \
                                    ubuntu@$EC2_HOST:/tmp/index.html

                                echo "===== Deploy Website ====="

                                ssh -i "$SSH_KEY" \
                                    -o StrictHostKeyChecking=no \
                                    ubuntu@$EC2_HOST '
                                        set -eux

                                        sudo mkdir -p /var/www/html

                                        sudo cp /tmp/index.html /var/www/html/index.html

                                        sudo chmod 644 /var/www/html/index.html

                                        sudo systemctl restart apache2

                                        sudo systemctl status apache2 --no-pager

                                        ls -l /var/www/html/index.html

                                        echo "Deployment completed successfully."
                                    '
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
