pipeline {
    agent { label "Jenkins-agent" }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag')
    }

    environment {
        APP_NAME = "register-app-pipeline"
        DOCKER_USER = "cyruss07"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout GitOps Repo") {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/mahamadhusen-ce/gitops-register-app'
            }
        }

        stage("Update Deployment YAML") {
            steps {
                sh """
                echo "Before update:"
                cat deployment.yaml

                sed -i "s|image:.*|image: ${IMAGE_NAME}:${IMAGE_TAG}|g" deployment.yaml

                echo "After update:"
                cat deployment.yaml
                """
            }
        }

        stage("Push Changes to Git") {
            steps {
                sh """
                git config --global user.name "mahamadhusen-ce"
                git config --global user.email "07kureshi@gmail.com"

                git add deployment.yaml
                git commit -m "Updated image to ${IMAGE_TAG}" || echo "No changes"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push origin main"
                }
            }
        }
    }

    post {
        success {
            echo "✅ CD Pipeline completed — ArgoCD auto deploy करेगा 🚀"
        }
        failure {
            echo "❌ CD Pipeline failed"
        }
    }
}
