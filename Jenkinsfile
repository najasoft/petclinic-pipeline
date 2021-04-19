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

        stage('SonarQube analysis') { steps {
    def scannerHome = tool 'SonarScanner 4.0';
        withSonarQubeEnv('sonar') { // If you have configured more than one global server connection, you can specify its name
                    sh "${scannerHome}/bin/sonar-scanner"
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
