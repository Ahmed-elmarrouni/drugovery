pipeline {
    agent any
    
    stages {
        stage('Build Project') {
            steps {
                echo 'Building backend and frontend containers...'
                sh 'docker compose build'
            }
        }
        
        stage('Test Backend') {
            steps {
                echo 'Running Python tests...'
                sh 'docker compose run --rm backend pytest tests/'
            }
        }
    }
}