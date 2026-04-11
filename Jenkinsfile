pipeline {
    agent any
    
    stages {
        stage('Build Project') {
            steps {
                echo 'Building containers...'
                sh 'docker compose -f docker-compose.yml -p drugovery-ci build'
            }
        }
        
        stage('Test Backend') {
            steps {
                echo 'Running Python tests...'
                sh 'docker compose -f docker-compose.yml -p drugovery-ci run -T --rm backend pytest tests/'
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo 'Logging into Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PWD', usernameVariable: 'DOCKER_USER')]) {
                    

                    sh "docker logout"
                    
                    sh "echo \$DOCKER_PWD | docker login -u \$DOCKER_USER --password-stdin"
                    
                    echo 'Tagging Images for Docker Hub...'
                    sh "docker tag drugovery-ci-backend \$DOCKER_USER/drugovery-backend:latest"
                    sh "docker tag drugovery-ci-frontend \$DOCKER_USER/drugovery-frontend:latest"
                    sh "docker tag drugovery-ci-celery-worker \$DOCKER_USER/drugovery-celery-worker:latest"
                    
                    echo 'Pushing Images to Docker Hub...'
                    sh "docker push \$DOCKER_USER/drugovery-backend:latest"
                    sh "docker push \$DOCKER_USER/drugovery-frontend:latest"
                    sh "docker push \$DOCKER_USER/drugovery-celery-worker:latest"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up containers...'
            sh 'docker compose -f docker-compose.yml -p drugovery-ci down -v'
        }
    }
}