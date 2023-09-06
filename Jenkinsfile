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
                withCredentials([usernamePassword(credentialsId: 'dockerhub1', passwordVariable: 'dockerhubpass', usernameVariable: 'dockerhubuser')]) {
                sh "docker tag my-note-app ${env.dockerhubuser}/my-note-app:latest"
                sh "docker login -u ${env.dockerhubuser} -p ${env.dockerhubpass}"
                sh "docker push ${env.dockerhubuser}/my-note-app:latest"
                }
            }
        }
        stage("Deploy"){
            steps {
                echo "Deploying the container"
                withCredentials([sshUserPrivateKey(credentialsId: 'nginx-server', keyFileVariable: 'nginx-server', passphraseVariable: 'nginx-pass', usernameVariable: 'nginx-user')]) {
                sshagent(['nginx-server']) {
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.127.96.24"
                    withCredentials([usernamePassword(credentialsId: 'dockerhub1', passwordVariable: 'dockerhubpass', usernameVariable: 'dockerhubuser')]) {
                    sh "docker login -u ${env.dockerhubuser} -p ${env.dockerhubpass}"
                    sh "docker pull ${env.dockerhubuser}/my-note-app:latest"
                    //sh "docker-compose down && docker-compose up -d"
                    }
                  }
                }      
            }
        }
    }
}
