pipeline {
    agent any
    
    stages {
        stage('Build Project') {
            steps {
                echo 'Building containers...'
                sh 'docker compose -p drugovery-ci build'
            }
        }
        
        stage('Test Backend') {
            steps {
                echo 'Running Python tests...'
                sh 'docker compose -p drugovery-ci run -T --rm backend pytest tests/'
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up containers...'
            sh 'docker compose -p drugovery-ci down'
        }
    }
}