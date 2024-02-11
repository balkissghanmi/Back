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
                }
            }
        }

        // Added Static Analysis Stage
        stage('Static Analysis') {
            parallel {
                stage('CodeSniffer') {
                    steps {
                        sh 'vendor/bin/phpcs --standard=phpcs.xml .'
                    }
                }
                stage('PHP Compatibility Checks') {
                    steps {
                        sh 'vendor/bin/phpcs --standard=phpcs-compatibility.xml .'
                    }
                }
                stage('PHPStan') {
                    steps {
                        sh 'vendor/bin/phpstan analyse --error-format=checkstyle --no-progress -c phpstan.neon . > build/logs/phpstan.checkstyle.xml'
                    }
                }
            }
        }

        // Existing Unit Tests Stage
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
            }
        }
    }

    post {
        always {
            recordIssues([
                sourceCodeEncoding: 'UTF-8',
                enabledForFailure: true,
                aggregatingResults: true,
                blameDisabled: true,
                referenceJobName: "Back/master", // Change "Back" to match your repository name
                tools: [
                    phpCodeSniffer(id: 'phpcs', name: 'CodeSniffer', pattern: 'build/logs/phpcs.checkstyle.xml', reportEncoding: 'UTF-8'),
                    phpStan(id: 'phpstan', name: 'PHPStan', pattern: 'build/logs/phpstan.checkstyle.xml', reportEncoding: 'UTF-8'),
                    phpCodeSniffer(id: 'phpcompat', name: 'PHP Compatibility', pattern: 'build/logs/phpcs-compatibility.checkstyle.xml', reportEncoding: 'UTF-8')
                ]
            ])
        }
    }
}
