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
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                        sh 'pwd'
                        sh 'ls'
                        sh 'hostname'
                    
                } 
            }
        }
    }
    //      stage('SonarQube Analysis') {
    //         steps {
    //             script {
    //                 def scannerHome= tool 'sonarqube-scanner-latest'
    //                 withSonarQubeEnv('sonarqube-scanner') { 
    //                     sh "${scannerHome}/bin/sonar-scanner"
                    
    //             } 
    //         }
    //     }
    // }

}
}
