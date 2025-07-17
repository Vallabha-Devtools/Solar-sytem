pipeline {
    agent any
 
    tools {
        nodejs 'nodejs-24-1-0'
    }
    // environment {
    //     MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
    // }
 
    stages{
        stage('Installing Deps') {
            options { timestamps() }
            steps {
                sh 'npm install --no-audit'
            }
        }
 
        stage('Dependency Scanning') {
            parallel {
                stage('NPM Dep Audit') {
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
                            --scan ./ \
                            --out ./ \
                            --format ALL \
                            --prettyPrint
                        ''', odcInstallation: 'OWASP-depcheck-12'
 
                        junit allowEmptyResults: true, stdioRetention: 'ALL', testResults: 'dependency-check-junit.xml'
                       
                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-jenkins.html', reportName: 'Dependency CheckHTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }      
                }
            }
        }
