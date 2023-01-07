pipeline {
    agent any
    triggers {
        pollSCM('* * * * *')
    }

    stages {
        // stage('Checkout') {
        //     steps {
        //         git url: 'https://github.com/rmarliere/jgsu-spring-petclinic.git', 
        //         branch: 'main'
        //     }
        // }
        stage('Build') {
            steps {
                sh "./mvnw clean package -Djacoco.skip=true"
                //sh 'false'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }
                changed {
                    emailext subject: "Job \'${JOB_NAME}\' (build ${BUILD_NUMBER}) ${currentBuild.result}", 
                        body: "Please go ${BUILD_URL} and verify the build", 
                        attachLog: true, 
                        compressLog: true,
                        to: "test@jenkins", 
                        recipientProviders: [requestor(), upstreamDevelopers()] 
                }
            }
        }
    }
}