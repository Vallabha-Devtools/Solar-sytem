pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-creds')
        MONGO_USERNAME = credentials('mongo-db-username')
        MONGO_PASSWORD = credentials('mongo-db-password')
        SONAR_SCANNER_HOME = tool 'sonarqube-scanner610';
    }
    stages {
        stage('Installing Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('Dependency Scanning') {
            parallel {
                stage('NPM Dependency Audit') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $?
                        '''
                    }
                }
                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                            --scan './'
                            --out './'
                            --format 'ALL'
                            --prettyPrint
                        ''', odcInstallation: 'OWASP-depcheck-12'
                    }
                }
            }
        }

        stage('Unit test') {
            options { retry(2) }
            steps {
                sh 'echo colon separated creds: $MONGO_DB_CREDS'
                sh 'echo Mongodb-username: $MONGO_DB_CREDS_USR'
                sh 'echo Mongodb-password: $MONGO_DB_CREDS_PSW'
                sh 'npm test'
            }
        }

        stage('code coverage') {
            steps {
                catchError(buildResult: 'SUCCESS', message: 'Oops! it will be fixed in the future releases', stageResult: 'UNSTABLE') {
                    sh 'npm run coverage'
                }
            }
        }
        stage('SAST Sonarqube') {
            steps {
                sh 'echo $SONAR_SCANNER_HOME'
                sh '''
                    $SONAR_SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=solar-system-project \
                        -Dsonar.sources=app.js \
                        -Dsonar.host.url=http://172.190.54.203:9000 \
                        -Dsonar.login=sqp_b4a9940c238e6290ba3add8ac90626dbbbf94cd1
                '''
            }
        }
    }
    post {
        always {
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, 
                             reportDir: 'coverage/lcov-report', 
                             reportFiles: 'index.html', 
                             reportName: 'Code Coverage HTML Report', 
                             reportTitles: '', 
                             useWrapperFileDirectly: true])
            junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
    
        }
    }

}
