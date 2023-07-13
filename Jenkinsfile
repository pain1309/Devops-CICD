pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        // SCANNER_HOME= tool 'sonar-scanner'
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
        stage('DOCKER BUILD and PUSH') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'e1b7a014-cebe-4a63-b37d-0b8dbe512bb9', toolName: 'docker-latest') {
                        sh "docker build -t cicddevops:latest ."
                        sh "docker tag cicddevops:latest vuvananh/cicddevops:latest"
                        sh "docker push vuvananh/cicddevops:latest"
                        // sh "docker image build -t ${REPOSITORY_TAG} ."
                        // sh "docker tag ${REPOSITORY_TAG} ${REPOSITORY_TAG}"
                        // sh "docker push ${REPOSITORY_TAG}"
                    }
                }
            }
        }
        stage('DEPLOY TO K8s') {
            steps {
                script {
                    withKubeConfig([credentialsId: "0bf6c843-8283-4086-bbfd-5527995dccf5"]) {
                        // sh 'kubectl delete pod -l app=spring-boot-k8s'
                        sh 'kubectl apply -f $JENKINS_HOME/workspace/CICD-github-webhook/deploymentservice.yaml'
                    }
                }
            }
        }
    }
}
