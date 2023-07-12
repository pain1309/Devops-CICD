pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
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
        stage('DOCKER BUILD') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'e1b7a014-cebe-4a63-b37d-0b8dbe512bb9', toolName: 'docker-latest') {
                        sh "docker build -t cicddevops ."
                    }
                }
            }
        }
        stage('DOCKER PUSH') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'e1b7a014-cebe-4a63-b37d-0b8dbe512bb9', toolName: 'docker-latest') {
                        sh "docker tag cicddevops vuvananh/cicddevops:latest"
                        sh "docker push vuvananh/cicddevops:latest"
                    }
                }
            }
        }
        stage('DEPLOY TO K8s') {
            steps {
                script {
                    withKubeConfig([credentialsId: "fec0d395-f8d3-43ff-a2ff-5a6ad7d2e982"]) {
                        sh 'kubectl patch deployment spring-boot-k8s-deployment -p "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"build\":\"${BUILD_ID}\"}}}}}"'
                        sh 'kubectl apply -f $JENKINS_HOME/workspace/CICD-github-webhook/deploymentservice.yaml'
                    }
                }
            }
        }
    }
}
