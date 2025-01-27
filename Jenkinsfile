pipeline {
    agent any
    stages {
        stage('GIT') {https://github.com/najasoft/spring-petclinic.git
            steps{
                git url: 'https://github.com/najasoft/spring-petclinic.git',branch:'devops1'
            }
        }
        stage('Build') {
            steps {
                sh "./mvnw package"

            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                  //  slackSend color: "danger", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was succeeded"
        
                }
               /* notBuilt {
                   
                   slackSend color: "danger", message: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was failed"
                } */
                
            }
        }

       stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonar') {        
                        sh './mvnw sonar:sonar'
                    
                }
            }
        }
         stage("Quality Gate") {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
            
        stage('Coverage'){
            steps{
                step( [ $class: 'JacocoPublisher' ] )
            }
        }
        stage('Checkstyle') {
                    steps {
                        sh "./mvnw checkstyle:checkstyle"
                        recordIssues(tools: [checkStyle(reportEncoding: 'UTF-8')])
                        
                    }
        }
        stage ('Email notification'){
            steps {
            mail bcc: '', body: "Job: ${env.JOB_NAME} with buildnumber ${env.BUILD_NUMBER} was succeeded" , cc: '', from: '', replyTo: '', subject: 'Jenkuns Job', to: 'najasoft@gmail.com'
            }
        }
    }
}
