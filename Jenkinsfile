pipeline{
    agent{
        label 'fortify-sca'
    }
    environment {
        ARGS = "-Dcom.fortify.WorkingDirectory=/home/jenkins -Dcom.fortify.sca.ProjectRoot=/home/jenkins"
    }

    stages{
       stage('Fortify Update'){
            steps{
                sh "fortifyupdate -acceptKey -includeRules -url https://sscdevops01.mmm.com:8443/ssc"
            }
        }

        stage('Maven Package'){
            steps{
                sh "mvn package"
            }
        }
      
        stage('Fortify Clean'){
            steps{
                //sh "sourceanalyzer -b ${BUILD_NUMBER} src/**/*.ts -Dcom.fortify.sca.Phase0HigherOrder.Languages=javascript,typescript ${ARGS}"
                sh "sourceanalyzer -b ${BUILD_NUMBER} -clean"
            }
        }
        stage('List Files' ){
            steps{
                sh "ls -la /home/jenkins/workspace/fortify-sai/target/WebGoat-6.0.1/WEB-INF/classes/org/owasp/webgoat"
            }
        }
        stage('Fortify Scan' ){
            steps{
                sh "sourceanalyzer -b ${BUILD_NUMBER} -cp 'WebGoat-Legacy/target/*.jar' target ${ARGS}"
                sh "sourceanalyzer -b ${BUILD_NUMBER} -scan -f results.fpr ${ARGS}"
            }
        }
        
        stage('Fortify Upload Report to SSC'){
            steps{
                withCredentials([string(credentialsId: 'fortify-token', variable: 'token')]) {
                    sh "fortifyclient -url https://sscdevops01.mmm.com:8443/ssc -authtoken ${token} uploadFPR -file results.fpr -applicationVersionID 10707"
                }
            }
        }
    }
}
