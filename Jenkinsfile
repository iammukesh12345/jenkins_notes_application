@Library("shared") _

pipeline {
    agent { label "magent_1" }
    stages {
        stage("Hello"){
            steps {
                script{
                    hello()
                }
            }
        }
        stage("Clone Code") {
            steps {
                cleanWs()
                script{
                    clone("https://github.com/iammukesh12345/jenkins_notes_application.git","main")
                }
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
                script{
                    build("notes-app","latest","mrthakre")
                }
               
            }
        }
        stage("PUSH IMAGE") {
            steps {
              script{
                  push("notes-app","latest","mrthakre")
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
                script{
                    deploy("notes-app:latest","notes-app","8000:8000")
                }
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
