pipeline {
    agent any

    stages {
        stage('Checkout Back GIT') {
            steps {
                git branch: 'main',
                    credentialsId: 'test',
                    url: 'https://github.com/balkissghanmi/Back.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'composer install --no-interaction --prefer-dist'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'php artisan test'
                }
            }
        }
    }
}
