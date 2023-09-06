pipeline {
    agent any 
    
    stages{
        stage("Clone Code"){
            steps {
                echo "Cloning the code"
                git url:"https://github.com/LondheShubham153/django-notes-app.git", branch: "main"
            }
        }
        stage("Build"){
            steps {
                echo "Building the image"
                sh "docker build -t my-note-app ."
            }
        }
        stage("Push to Docker Hub"){
            steps {
                echo "Pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'docker-pass', usernameVariable: 'dockerhub-user')]) {
                sh "docker tag my-note-app ${env.dockerhub-user}/my-note-app:latest"
                sh "docker login -u ${env.dockerhub-user} -p ${env.docker-pass}"
                sh "docker push ${env.dockerhub-user}/my-note-app:latest"
                }
            }
        }
        stage("Deploy"){
            steps {
                echo "Deploying the container"
                withCredentials([sshUserPrivateKey(credentialsId: 'nginx-server', keyFileVariable: 'nginx-server', passphraseVariable: 'nginx-pass', usernameVariable: 'nginx-user')]) {
                    sh "docker login -u ${env.dockerhub-user} -p ${env.docker-pass}"
                    sh "docker pull ${env.dockerhub-user}/my-note-app:latest"
                    //sh "docker-compose down && docker-compose up -d"
                }
            }
        }
    }
}
