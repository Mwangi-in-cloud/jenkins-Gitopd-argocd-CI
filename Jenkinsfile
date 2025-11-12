pipeline {
    agent any
    environment {
        IMAGE = "docker.io/cronosm4m/jenkargo"
        TAG = "${env.BUILD_NUMBER}"
        GIT_TOKEN = credentials('GIT_TOKEN')
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
                       
                       echo "cloning CD repository"
                       rm -rf gitops
                       git clone https://github.com/Mwangi-in-cloud/jenkins-Gitops-argocd-CD.git gitops
                       
                       echo "updating the image tag"
                       cd gitops/manifests
                       sed -i "s|image: .*/cronosm4m/jenkargo:.*|image: ${IMAGE}:${TAG}|g" cd.yml

                       echo "Committing and pushing changes..."
                       cd ..
                       git add manifests/cd.yml
                       git commit -m "Update image to ${BUILD_NUMBER}" || echo "No changes to commit"
                       git push origin main

                    """
                }
            }
        }
    }
}
