pipeline {
    agent any

    



    stages {
        
        stage('Build') {
            steps {
                sh 'npm install'
                
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm run test'
                sh 'npm run ci-test'
                     
            }

            post {
                always {
                    step([$class: "TapPublisher", testResults: "test.tap"])
                    step([
                        $class: 'CloverPublisher',
                        cloverReportDir: 'coverage/',
                        cloverReportFileName: 'clover.xml',
                        healthyTarget: [methodCoverage: 70, conditionalCoverage: 70, statementCoverage: 70], // optional, default is: method=70, conditional=80, statement=80
                        unhealthyTarget: [methodCoverage: 50, conditionalCoverage: 50, statementCoverage: 50], // optional, default is none
                        failingTarget: [methodCoverage: 0, conditionalCoverage: 0, statementCoverage: 0]     // optional, default is none
                        ])
                }
            }
        }

        stage('trivy'){
            steps{
                sh 'trivy image -f json -o trivy-results.json debian:10.8'
            }
             post{
                always{
                    
                    recordIssues enabledForFailure: true, tool: trivy(pattern: 'trivy-results.json')
                }
            }
           
            
        }

    }
}

