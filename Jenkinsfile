pipeline {
    agent any

    stages {

        stage('Test') {
            steps {
sh 'grep -q "THIS-DOES-NOT-EXIST" index.html'
echo 'Test passed: CI/CD text found'            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-cicd-demo .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker rm -f devops-cicd-demo-container || true

                    docker run -d \
                        --name devops-cicd-demo-container \
                        -p 8082:80 \
                        devops-cicd-demo:latest
                '''
            }
        }

    }
}
