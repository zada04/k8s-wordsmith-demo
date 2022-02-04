pipeline {
    agent any
    environment {
        DOCKER_IMAGE_DB = "zada04/words-db"
        DOCKER_IMAGE_WEB = "zada04/words-web"
        DOCKER_IMAGE_APP = "zada04/words-app" 
    }
          stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_APP)
                    db = docker.build(DOCKER_IMAGE_DB)
                    web = docker.build(DOCKER_IMAGE_WEB)                    
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {                        
                        app.push("latest")
                        db.push("latest")
                        web.push("latest")  
                    } 
                }
            }
        }      
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'minikube_config',
                    configs: 'kube-deployment.yml',
                    enableConfigSubstitution: true
                )              
            }
        }
    }

