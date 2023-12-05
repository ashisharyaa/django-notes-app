pipeline {
    agent any
    
    stages {
        stage("Clone code") {
            steps {
                echo "Clone code from the GitHub repository"
                git url: "https://github.com/S47sawan/django-notes-app.git", branch: "main"
            }
        }
        
        stage("Build") {
            steps {
                echo "Building Image"
                sh "docker build -t my-notes-app ."
            }
        }
        
        stage("Scan") {
            steps {
                echo "Scan Image with Trivy"
                sh "trivy image smihah/my-notes-app:latest --scanners vuln"
            }
        }
        
        stage("Push to DockerHub") {
            steps {
                echo "Push build image to DockerHub"
        
                // Use withCredentials block to securely handle DockerHub credentials
                withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag my-notes-app:latest ${env.dockerHubUser}/my-notes-app:latest"
                    sh "docker push ${env.dockerHubUser}/my-notes-app:latest"
                }
            }
        }
        
       stage("Deploy") {
          steps {
                echo "Deploy image from Docker Hub to AWS EC2"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
