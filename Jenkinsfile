pipeline {
  agent any
  stages {
    stage('Docker Build') {
      steps {
        sh "docker build -t dockerabbaas/poc-app:${env.BUILD_NUMBER} ."
      }
    }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push dockerabbaas/poc-app:${env.BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Remove Image') {
      steps {
        sh "docker rmi dockerabbaas/poc-app:${env.BUILD_NUMBER}"
      }
    }
    stage('Deploy To Kubernetes') {
      steps {
          withKubeConfig([credentialsId: 'k8s-api-token', serverUrl: 'https://api.smkbservice.tk']) {
          sh 'cat deploy.yml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g"'
          sh 'kubectl apply -f .'
        }
      }
  }
}
post {
    success {
      slackSend(message: "Pipeline is successfully completed.")
    }
    failure {
      slackSend(message: "Pipeline failed. Please check the logs.")
    }
}
}
