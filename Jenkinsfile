pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.8 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker stop nginx || echo "nginx not running"
                docker rm nginx || echo "nginx not running"

                docker rmi haideralam/python-api || echo "Image does not exist"
                docker rmi haideralam/flask-nginx || echo "Image does not exist"
                docker network create project || echo "network already exists"
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t haideralam/python-api -t haideralam/python-api:v${BUILD_NUMBER} .
                docker built -t haideralam/flask-nginx -t haideralam/flask-nginx:v${BUILD_NUMBER} ./nginx               
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push haideralam/python-api
                docker push haideralam/python-api:v${BUILD_NUMBER}
                docker push haideralam/flask-nginx
                docker push haideralam/flask-nginx:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.8 << EOF
                docker run -d -p --name flask-app --network project haideralam/python-api
                docker run -d -p 80:80 --name nginx --network project haideralam/flask-nginx
                '''
            }
        }
        stage('Cleanup') {
            steps {
                sh '''
                docker system prune -f 
                docker rmi haideralam/python-api:v${BUILD_NUMBER}
                docker rmi haideralam/nginx:v${BUILD_NUMBER}
                '''
           }
        }
    }
}
