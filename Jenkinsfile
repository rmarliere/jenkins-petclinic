pipeline {
    agent any
    environment {
        IMAGE_REPO_NAME="springpetclinic"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "https://775591165938.dkr.ecr.us-east-1.amazonaws.com"
    }
    // triggers {
    //     pollSCM('* * * * *')
    // }

    stages {
        stage('Build') {
            steps {
                sh "./mvnw clean package -Djacoco.skip=true"
                //sh 'true'
            }
            post {
                always {
                    //junit 'target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }
                // changed {
                //     emailext subject: "Job \'${JOB_NAME}\' (build ${BUILD_NUMBER}) ${currentBuild.result}", 
                //         body: "Please go ${BUILD_URL} and verify the build", 
                //         attachLog: true, 
                //         compressLog: true,
                //         to: "test@jenkins", 
                //         recipientProviders: [requestor(), upstreamDevelopers()] 
                // }
            }
        }
        stage('Docker Build and Push') {
            steps {
                script {
                    docker.withRegistry("${REPOSITORY_URI}", "ecr:us-east-1:awskeys") {
                        def dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy'){
            steps {
                sh 'kubectl apply -f manifests/deployment.yml'
            }
        }
    }
}