pipeline {
    agent any

    environment {
        IMAGE_NAME = "static-website"
        REGISTRY   = "localhost:5001"
        TAG        = "v${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
            steps {
                echo 'Clonage du repo...'
                git url: 'https://github.com/makuissu/static-website-example',
                    branch: 'master'
            }
        }

        stage('Create Dockerfile') {
            steps {
                echo 'Creation du Dockerfile...'
                sh '''
                    cat > Dockerfile << EOF
FROM nginx:alpine
COPY . /usr/share/nginx/html
EXPOSE 80
EOF
                '''
            }
        }

        stage('Build Image') {
            steps {
                echo 'Build de l image Docker...'
                sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Push Registry') {
            steps {
                echo 'Push vers le registry local...'
                sh "docker push ${REGISTRY}/${IMAGE_NAME}:${TAG}"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploiement du site...'
                sh """
                    docker stop static-website || true
                    docker rm   static-website || true
                    docker run -d \
                        --name static-website \
                        -p 8084:80 \
                        ${REGISTRY}/${IMAGE_NAME}:${TAG}
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline termine avec succes ! Site dispo sur http://localhost:8084'
        }
        failure {
            echo 'Le pipeline a echoue.'
        }
    }
}
