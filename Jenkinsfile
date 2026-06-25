pipeline {

    agent any

    environment {
        EC2_HOST = "ubuntu@54.221.144.84"
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Package') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Deploy Test') {
            steps {
                sshagent(['ec2-ssh']) {
                    bat 'ssh -o StrictHostKeyChecking=no ubuntu@54.221.144.84 "echo Connected"'
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['ec2-ssh']) {
                    bat """
                    ssh ubuntu@54.221.144.84 ^
                    "cd ~/Spring-AI && git pull && mvn clean package && \
                    pkill -f SpringAIDemo || true && \
                    nohup java -jar target/*.jar --server.address=0.0.0.0 --server.port=8080 > app.log 2>&1 &"
                    """
                }
            }
        }
    }
}