pipeline {

    agent any
    stages {
        stage('Docker Build') {
            steps {
                sh "docker build -t pareshbarad1992/node-app:${env.BUILD_NUMBER} ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                    sh "docker push pareshbarad1992/node-app:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Docker Remove Image') {
            steps {
                sh "docker rmi pareshbarad1992/node-app:${env.BUILD_NUMBER}"
            }
        }
        
        stage('Apply Kubernetes Files') {
        steps {
            withKubeConfig() {
                sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
                sh 'kubectl apply -f service.yaml'
            }
        }
  }

    }
    post {
        success {
            echo 'Pipeline is successfully completed.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
        always {
            sh 'docker logout'
        }
    }
}