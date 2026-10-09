pipeline {
agent any

stages {
stage ('Best') {
steps {
sh 'grep -q "CI/CD" index.html'
}
}
 stage ('Build') {
 steps {
sh 'docker build -t website:${BUILD_NUMBER} .'
}
}

stage('Deploy') {
steps {
sh '''
docker rm -f website-container || true
docker run -d --name website-container -p 8082:80 website:${BUILD_NUMBER}
'''
}
}

stage('Health Check') {
steps {
sh '''
set -e
curl --fail --silent http://localhost:8080 -o response.html
 if grep -q "CI/CD" response.html; then
 echo 'health check passed'
else
echo 'health check failed'
exit 1
fi
'''
}
}
}
}
