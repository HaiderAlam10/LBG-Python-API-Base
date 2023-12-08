pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.9 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t haideralam/python-api -t haideralam/python-api:v${BUILD_NUMBER} .                  
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push haideralam/python-api
                docker push haideralam/python-api:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.9 << EOF
                docker run -d -p 80:8080 --name flask-app haideralam/python-api
                '''
            }
        }
        stage('Cleanup') {
            steps {
                sh '''
                docker system prune -f 
                '''
           }
        }
    }
}
