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
             environment {
           XDEBUG_MODE = 'coverage'
        }
         steps {
                script {
                    sh 'cp .env.example .env'
                    sh 'php artisan key:generate'
                    sh 'php artisan test'
                   // sh 'vendor/bin/phpunit --log-junit test-results.xml' // Run PHPUnit tests and generate a JUnit XML report
                }
            }
        }
        stage('Unit Tests') {
    steps {
         environment {
           XDEBUG_MODE = 'coverage'
        }
        sh 'vendor/bin/phpunit'
        xunit([
            thresholds: [
                failed ( failureThreshold: "0" ),
                skipped ( unstableThreshold: "0" )
            ],
            tools: [
                PHPUnit(pattern: 'build/logs/junit.xml', stopProcessingIfError: true, failIfNotNew: true)
            ]
        ])
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: false,
            reportDir: 'build/coverage',
            reportFiles: 'index.html',
            reportName: 'Coverage Report (HTML)',
            reportTitles: ''
        ])
        discoverGitReferenceBuild()
        recordCoverage(tools: [[parser: 'COBERTURA', pattern: 'build/logs/cobertura.xml']])
    }
}
    }
}
