pipeline {
    agent any
    environment {
        IMAGE = "docker.io/cronosm4m/jenkargo"
        TAG = "${env.BUILD_NUMBER}"
        GIT_TOKEN = credentials('repo-token')
    }
    stages {
        stage ("checking out repo") {
            steps {
               git branch: 'main', credentialsId: 'GIT-CREDS', url: 'https://github.com/Mwangi-in-cloud/jenkins-Gitopd-argocd-CI.git' 
            }
        }
        stage ("building the docker image") {
            steps {
                sh 'docker build -t "$IMAGE:$TAG" .'
            }
        }
        stage ("trivy scannin now") {
            steps {
                sh """
                 #trivy image --severity HIGH,CRITICAL --no-progress --format table -o trivy-scan-report.txt ${IMAGE}:${TAG}
                 echo "skipping this now"
                """
            }
        }
        stage ("now to dockerhub") {
            steps {
                  withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'DOCKERHUB_PWD', usernameVariable: 'DOCKERHUB_USER')]) {
                   sh 'echo "$DOCKERHUB_PWD" | docker login -u "$DOCKERHUB_USER" --password-stdin'
                   sh 'docker push "$IMAGE:$TAG"'
                  }
            }
        }
        stage ("now to CD PART") {
            steps {
                script {
                    sh """
                       echo "Configuring Git..."
                       git config --global user.name "Jenkins"
                       git config --global user.email "jenkins@server"
                       git config --global credential.helper store  
                       echo "https://${GIT_TOKEN}:x-oauth-basic@github.com" > ~/.git-credentials


                    """
                }
            }
        }
    }
}
