pipeline {
    agent any

    stages {
        stage('SCM_CHECKOUT') {
            steps {
                git 'https://github.com/MAHENDRAN-ANBAZHAGAN/my-app'
            }
        }
        stage('SonarQube Analysis') {
            steps {
              script {
             withSonarQubeEnv(credentialsId: 'SonarQube_token') {
                 
              sh 'mvn clean verify sonar:sonar'
             }
            }
            
            }
        }
            stage('Maven Compile') {
            steps {
                sh 'whoami'
                sh script: 'mvn clean install'
                sh 'mv target/myweb*.war target/newapp.war'
            }
        }
            stage('Build Docker Image') {
            steps {
                script {
                    
                    sh 'cd /var/lib/jenkins/workspace/project'
                    def dockerImage = docker.build("mahendran1996anbazhagan/myweb:0.0.2", "--file Dockerfile .")
                }
            }
        }
            stage('Push Image to ECR') {
            steps {
                script {
                    docker.withRegistry(
                        'https://174047634073.dkr.ecr.ap-southeast-2.amazonaws.com',
                        'ecr:ap-southeast-2:my.aws.credentials') {
                            def myImage = docker.build('anbu')
                            myImage.push('latest')
                        }
                    }
                }
        }
            stage('EKS Deploy') {
            steps {
                
                echo 'Deploying on EKS'
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '']]) {
               
                 sh 'kubectl apply -f /var/lib/jenkins/mainservice.yaml'
             }
            }
        }
    }
}
