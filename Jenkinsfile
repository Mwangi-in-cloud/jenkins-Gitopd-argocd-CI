pipeline {
    agent any
    environment {
        image = 'docker.io/cronosm4m/jenkargo'
        TAG = '${env.BUILD_NUMBER}'
    }
    stages {
        stage ("checking out repo") {
            steps {
               git branch: 'main', credentialsId: 'GIT-CREDS', url: 'https://github.com/Mwangi-in-cloud/jenkins-Gitopd-argocd-CI.git' 
            }
        }
        stage ("building docker image") {
            steps {
                sh 'docker build -t "$IMAGE:$TAG"'
            }
        }
    }
}
