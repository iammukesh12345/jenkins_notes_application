pipeline {
    agent { label "magent_1" }
    stages {
        stage("Clone Code") {
            steps {
                cleanWs()
                echo "Cloning repository..."
                git url: "https://github.com/iammukesh12345/jenkins_notes_application.git", branch: "main"
                echo "Code cloning success"
            }
        }
         stage("Workspace Optimization") {
            steps {
                echo "Optimizing workspace (removing heavy folders)..."
                sh '''
                    rm -rf .git
                    rm -rf node_modules target || true
                '''
            }
        }
        stage("Build Docker Image") {
            steps {
                echo "Building Docker image..."
                sh "docker build -t notes-app:latest ."
            }
        }
        stage("PUSH IMAGE") {
            steps {
                echo "Pushing docker image on Docker hub"
                withCredentials([usernamePassword('credentialsId':"dockerhub_cred",
                                                   passwordVariable:"dockerHubPass",
                                                   usernameVariable:"dockerHubUser")])
                {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag notes-app:latest ${env.dockerHubUser}/notes-app:latest"
                    sh "docker push ${env.dockerHubUser}/notes-app:latest"
                }
            }
        }
        
        stage("Docker Cleanup") {
            steps {
                sh '''
                    docker container prune -f
                    docker image prune -f
                '''
            }
        }
        stage("Deploy Docker Container") {
            steps {
                echo "Stopping and removing old container if exists..."
                sh '''
                    docker rm -f notes-app || true
                    docker run -d --name notes-app -p 8000:8000 notes-app:latest
                '''
            }
        }
         stage("Health Check") {
            steps {
           sh "sleep 10"
           sh "curl -f http://localhost:8000 || exit 1"

    }
}
  }
   post {
        always {
            echo "Final workspace cleanup..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully 🚀"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
