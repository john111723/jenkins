pipeline {
    agent any
    
    environment {
        // Replace with your details
        DOCKERHUB_USER = 'john111723'
        IMAGE_NAME     = 'my-html-app'
        IMAGE_TAG      = "${env.BUILD_NUMBER}"
        GIT_REPO       = 'github.com/john111723/jenkins.git'
    }

    stages {
       

        stage('Push Changes to GitHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-token', passwordVariable: 'GIT_PASS', usernameVariable: 'GIT_USER')]) {
                    sh """
                        git config user.email "singhbee25@gmail.com"
                        git config user.name "Jenkins CI"
                        echo "Build automated by Jenkins" >> build-log.txt
                        git add .
                        git commit -m "Automated build update: ${env.BUILD_NUMBER}"
                        git push https://${GIT_USER}:${GIT_PASS}@${GIT_REPO} HEAD:main
                    """
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-credentials-id') {
                        def customImage = docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
                        customImage.push()
                        customImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-id']) {
                    sh """
                        kubectl set image deployment/my-html-deployment \
                        my-html-app=${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
