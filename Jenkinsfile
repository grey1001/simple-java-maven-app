pipeline {
    agent {
        label 'agent'
    }
    
    tools {
        maven 'mymaven'
        dockerTool 'mydocker' // Name of the Docker installation configured in Jenkins
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Build Docker Image') {
            agent {
                label 'qa_server'
            }
            
            steps {
                script {
                    def imageName = 'greyabiwon/my-app'
                    
                    docker.build(imageName, "-f Dockerfile .")
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-login') {
                        docker.image(imageName).push()
                    }
                }
            }
        }
        
        stage('Deploy to Container') {
            agent {
                label 'qa_server'
            }
            
            steps {
                script {
                    def containerName = 'my-app'
                    def imageName = 'greyabiwon/java-mvn-app:v4.0'
                    
                    sh "docker pull $imageName"
                    sh "docker stop $containerName || true"
                    sh "docker rm $containerName || true"
                    sh "docker run -d -p 8538:8080 --name $containerName $imageName"
                }
            }
        }
    }
}
