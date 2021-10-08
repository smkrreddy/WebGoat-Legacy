pipeline{
    agent{
        label 'fortify-sca'
    }
    environment {
        ARGS = "-Dcom.fortify.WorkingDirectory=. -Dcom.fortify.sca.ProjectRoot=."
    }

    stages{
       stage('Fortify Update'){
            steps{
                sh "fortifyupdate -acceptKey -includeRules -url https://sscdevops01.mmm.com:8443/ssc"
            }
        }
      
        stage('Fortify Clean'){
            steps{
                //sh "sourceanalyzer -b ${BUILD_NUMBER} src/**/*.ts -Dcom.fortify.sca.Phase0HigherOrder.Languages=javascript,typescript ${ARGS}"
                sh "sourceanalyzer -b WebGoat5.0 -clean"
            }
        }
        
        stage('Fortify Scan1' ){
            steps{
                sh "sourceanalyzer -b WebGoat5.0 -source 1.5 WebGoat-Legacy"
                //sh "sourceanalyzer -b ${BUILD_NUMBER} -scan -f results.fpr ${ARGS}"
            }
        }

        stage('Fortify Scan2' ){
            steps{
                sh "sourceanalyzer -b WebGoat5.0 -scan -f WebGoat5.0.fpr"
                //sh "sourceanalyzer -b ${BUILD_NUMBER} -scan -f results.fpr ${ARGS}"
            }
        }
        
        stage('Fortify Upload Report to SSC'){
            steps{
                withCredentials([string(credentialsId: 'fortify-token', variable: 'token')]) {
                    sh "fortifyclient -url https://sscdevops01.mmm.com:8443/ssc -authtoken ${token} uploadFPR -file results.fpr -applicationVersionID 10458"
                }
            }
        }
    }
}
