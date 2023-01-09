pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="222222222222"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="springpetclinic"
        IMAGE_TAG="v1"
        REPOSITORY_URI = "22222222222.dkr.ecr.us-east-1.amazonaws.com/jenkins-pipeline"
    }
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
                //sh 'true'
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
        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
      	        //sh 'docker build -t springpetclinic/dockerspringboot:latest .'
            }
        }
        stage('Push to ECR') {
            docker.withRegistry("775591165938.dkr.ecr.us-east-1.amazonaws.com/jenkins", "ecr:us-east-1:awskeys") {
                docker.image(dockerImage).push()
            }
        }
    }
}