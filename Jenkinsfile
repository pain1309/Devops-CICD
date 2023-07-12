pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        ORGANIZATION_NAME = "iris"
        YOUR_DOCKERHUB_USERNAME = "vuvananh"
        SERVICE_NAME = "java"
        REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }

    stages {
        stage('GIT CHECKOUT') {
            steps {
                git branch: 'main', url: 'https://github.com/pain1309/Devops-CICD.git'
            }
        }
        stage('CODE COMPILE') {
            steps {
                sh "mvn clean compile"
            }
        }
        //stage('SONARQUBE ANALYSIS') {
        //    steps {
        //        withSonarQubeEnv('sonar-scanner') {
        //            sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Devops-CICD \
        //           -Dsonar.java.binaries=. \
        //           -Dsonar.projectKey=Devops-CICD '''
        //        }
        //    }
        //}
        //stage('TRIVY SCAN') {
        //    steps {
        //        sh "trivy fs --security-checks vuln /var/lib/jenkins/workspace/CICD"
        //    }
        //}
        stage('CODE BUILD') {
            steps {
                sh " mvn clean install "
            }
        }
        // stage('DOCKER BUILD') {
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'e1b7a014-cebe-4a63-b37d-0b8dbe512bb9', toolName: 'docker-latest') {
        //                 sh "docker build -t cicddevops:${env.BUILD_ID} ."
        //             }
        //         }
        //     }
        // }
        stage('DOCKER BUILD and PUSH') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'e1b7a014-cebe-4a63-b37d-0b8dbe512bb9', toolName: 'docker-latest') {
                        // sh "docker tag cicddevops:${BUILD_ID} vuvananh/cicddevops:${BUILD_ID}"
                        // sh "docker push vuvananh/cicddevops:${env.BUILD_ID}"
                        sh "docker image build -t ${REPOSITORY_TAG} ."
                        sh "docker push ${REPOSITORY_TAG}"
                    }
                }
            }
        }
        stage('DEPLOY TO K8s') {
            steps {
                script {
                    withKubeConfig([credentialsId: "fec0d395-f8d3-43ff-a2ff-5a6ad7d2e982"]) {
                        sh 'kubectl apply -f $JENKINS_HOME/workspace/CICD-github-webhook/deploymentservice.yaml'
                    }
                }
            }
        }
    }
}
