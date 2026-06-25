pipeline {

```
agent any

environment {
    EC2_HOST = "ubuntu@54.221.144.84"
}

stages {

    stage('Build') {
        steps {
            sh 'mvn clean compile'
        }
    }

    stage('Test') {
        steps {
            sh 'mvn test'
        }
    }

    stage('Package') {
        steps {
            sh 'mvn clean package'
        }
    }

    stage('Deploy') {
        steps {

            sshagent(['ec2-ssh']) {

                sh '''
                ssh -o StrictHostKeyChecking=no $EC2_HOST '

                    cd ~/Spring-AI

                    git pull

                    mvn clean package

                    pkill -f SpringAIDemo || true

                    nohup java -jar target/*.jar \
                    --server.address=0.0.0.0 \
                    --server.port=8080 \
                    > app.log 2>&1 &

                    pkill -f vite || true

                    cd src/main/llm-comparison-ui

                    npm install

                    nohup npm run dev -- \
                    --host 0.0.0.0 \
                    --port 5173 \
                    > vite.log 2>&1 &
                '
                '''
            }
        }
    }
}
```

}
