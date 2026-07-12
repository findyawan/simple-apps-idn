pipeline {
    agent { label 'docker-server-farras' }
    
    tools {nodejs "nodejs-simpleapps"}

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: ''
            }
        }
        stage('Build') {
            steps {
                sh '''
                npm install'''
            }
        }
        stage('Testing') {
            steps {
                sh '''
                npm test
                npm run test:coverage'''
            }
        }
        stage('Code Review') {
            steps {
                sh '''
                sonar \
                  -Dsonar.host.url=http://172.23.11.111:9000 \
                  -Dsonar.token=sqp_3dec01f918cd71a919fa465d679eff14ce8f9b9c \
                  -Dsonar.projectKey=simple-apps'''
            }
        }
        stage('Deploy compose') {
            steps {
                sh '''
                docker compose build
                docker compose up -d
                '''
            }
        }
    }
}
