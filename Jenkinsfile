pipeline {
    agent any
   
    stages {
          stage('Clean Workspace') {
        steps {
         cleanWs()
    }
}
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

        // stage('PHPStan Analysis') {
        //     steps {
        //         script {
        //             sh 'mkdir -p reports'
        //             sh 'composer require --dev phpstan/phpstan'
        //             //sh 'vendor/bin/phpstan analyse public tests' //sh 'vendor/bin/phpstan analyse --level max --no-progress -c phpstan.neon .'
        //             sh ' vendor/bin/phpstan analyse -l 2 --no-progress -c phpstan.neon .'
        //             //sh 'vendor/bin/phpstan analyse -l 5 --no-progress -c phpstan.neon . --error-format=json > reports/phpstan-report.json'
        //             sh ' echo "test"'
        //         }
        //     }
        // }
        
        stage('Run Tests') {
            environment {
                XDEBUG_MODE = 'coverage'
            }
            steps {
                script {
                    sh 'cp .env.example .env'
                    sh 'php artisan key:generate'
                    sh 'php artisan test'
                    // sh 'vendor/bin/phpunit --log-junit test-results.xml' // Optionally run PHPUnit directly and generate a JUnit XML report
                    sh'vendor/bin/phpunit --coverage-clover build/logs/clover.xml'

                }
            }
        }

        stage('Unit Tests') {
            environment {
                XDEBUG_MODE = 'coverage'
            }
            steps {
                sh 'vendor/bin/phpunit'
                xunit([
                    thresholds: [
                        failed(failureThreshold: "0"),
                        skipped(unstableThreshold: "0")
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

         stage('SonarQube Analysis') {
            steps {
                script {
                         sh 'pwd'
                         sh  '/opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=jenkins-sonarqube-token -Dsonar.sources=. -Dsonar.host.url=http://192.168.56.20:9000 -Dsonar.login=sqa_ed405358e620f6865067d0bdc8c4a651ce6b3ce1 -Dsonar.php.coverage.reportPaths=build/logs/clover.xml'
                        //sh  '/opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=jenkins-sonarqube-token -Dsonar.sources=. -Dsonar.host.url=http://192.168.56.20:9000 -Dsonar.login=sqa_ed405358e620f6865067d0bdc8c4a651ce6b3ce1 -Dsonar.flex.cobertura.reportPaths=build/logs/cobertura.xml'
            }
        }
    }

}
}
