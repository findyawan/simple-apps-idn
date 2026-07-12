pipeline {
    agent { label 'docker-server-farras' }
    
    tools {nodejs "nodejs-18.16.0"}
    environment {
        // Tentukan nama image dan tag di sini
        DOCKER_HUB_USER = 'ras617'
        IMAGE_NAME      = 'simple-apps'
        // Menggunakan nomor build Jenkins agar tag selalu unik (misal: simple-apps:build-12)
        IMAGE_TAG       = "build-${BUILD_NUMBER}" 
        FULL_IMAGE      = "${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
        // sonarqube
        HOSTURL        = 'http://172.23.11.111:9000'
        S_TOKEN        = 'sqp_3dec01f918cd71a919fa465d679eff14ce8f9b9c'
    }
    stages {
        
        stage('Build') {
            steps {
                sh '''
                npm install'''
            }
        }
        
        stage('Code Review') {
            steps {
                sh '''
                sonar \
                  -Dsonar.host.url=${HOSTURL} \
                  -Dsonar.token=${S_TOKEN} \
                  -Dsonar.projectKey=${IMAGE_NAME}'''
            }
        }
        stage('Deploy Build & PUSH') {
            steps {
                sh '''
                # Build image dengan tag dinamis
                docker build -t $FULL_IMAGE .
                    
                # Push ke Docker Hub
                docker push $FULL_IMAGE
                '''
            }
        }
        stage('Deploy Compose') {
            steps {
                // Mengoper variabel ke docker compose
                sh '''
                IMAGE_TO_DEPLOY=${FULL_IMAGE} docker compose up -d --build
                '''
            }
        }
    }
    // Bagian pembersihan setelah pipeline selesai (sukses maupun gagal)
    post {
        always {
            sh '''
            echo "Membersihkan image lokal agar disk tidak penuh..."
            docker rmi $FULL_IMAGE || true
            docker image prune -f
            '''
        }
    }
}
