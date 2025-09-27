pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
 }
    environment{
       SCANNER_HOME= tool 'sonar-scanner'
   } 
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/farhanawan77/FullStack-Blogging-App.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        // stage('Trivy FS Scan') {
        //     steps {
        //         sh "trivy fs --format table -o trivy-report.html ."
                 
        //     }
        // }
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar-server') {
        //         sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app \
        //               -Dsonar.java.binaries=target'''
        //         }
        //     }
        // }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        // stage('Docke Image remove chache') {
        //     steps {
        //          sh 'docker system prune -af || true'
        //     }
        // }       

        // stage('Docker build & tag') {
        //     steps {
        //         script{
        //             withDockerRegistry(credentialsId: 'dockerhub-credentials', toolName: 'docker') {
        //             sh "docker build -t farhanawan99/bloggingapp:latest ."
        //             }
        //         }
        //     }
        // }
        
        stage('Docker build & tag') {
             steps {
                 script {
                    def imageTag = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    env.IMAGE_TAG = imageTag
                    sh "docker build -t farhanawan99/bloggingapp:${IMAGE_TAG} ."
        }
    }
}

        
        // stage('Trivy image scan') {
        //     steps {
        //         sh 'trivy fs image --format table -o trivy-image.html farhanawan99/bloggingapp:latest '
        //     }
        // }
        // stage('Dockr push image') {
        //     steps {
        //         script{
        //             withDockerRegistry(credentialsId: 'dockerhub-credentials', toolName: 'docker') {
        //             sh "docker push farhanawan99/bloggingapp:${IMAGE_TAG}"
        //             }
        //         }
        //     }
        // }
        stage('K8s Deploy') {
          steps {
            withCredentials([file(credentialsId: 'kubeconfig-jenkins', variable: 'KUBECONFIG_FILE')]) {
            sh '''
             export KUBECONFIG=$KUBECONFIG_FILE
             kubectl get nodes
             kubectl apply -f K8s/deployment.yaml
             kubectl apply -f K8s/service.yaml
              '''
}

             }
        }
        stage('Verify Deployment') {
            steps {
                withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                 sh '''
                    export KUBECONFIG=$KUBECONFIG_FILE
                    kubectl get pods -o wide
                     '''
}

  }
}


    }
}

