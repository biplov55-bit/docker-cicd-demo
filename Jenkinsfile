
pipeline {
    agent any

    parameters {
        string(
            name: 'DEPLOY_VERSION',
            defaultValue: '',
            description: 'Docker image version to deploy; leave blank to deploy this build'
        )
    }

    stages {

        stage('Test') {
            steps {
                sh 'grep -q "CI/CD" index.html'
                echo 'Test passed: CI/CD text found'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-cicd-demo:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def version = params.DEPLOY_VERSION?.trim()

                    if (!version) {
                        version = env.BUILD_NUMBER
                    }

                    if (!(version ==~ /^[0-9]+$/)) {
                        error('DEPLOY_VERSION must contain only digits')
                    }

                    sh """
                        docker image inspect devops-cicd-demo:${version} >/dev/null

                        docker rm -f devops-cicd-demo-container || true

                        docker run -d \
                            --name devops-cicd-demo-container \
                            -p 8082:80 \
                            devops-cicd-demo:${version}
                    """

                    echo "Deployed Docker image version: ${version}"
                }
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    for i in $(seq 1 10); do
                        if curl --fail --silent http://localhost:8082; then
                            echo "Health check passed!"
                            exit 0
                        fi
                        echo "Waiting for application..."
                        sleep 3
                    done

                    echo "Health check failed!"
                    exit 1
                '''
            }
        }
    }
}     
    
