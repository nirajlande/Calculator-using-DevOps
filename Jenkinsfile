pipeline {
    // Declare variables that will be used by the later stages
    environment {
        DOCKERHUB_REGISTRY = "nirajlande/calculator-using-devops"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-id')
    }
    
    // The "agent" section configures on which nodes the pipeline can be 
    // run. Specifying "agent any" means that Jenkins will run the job on  
    // any of the available nodes.
    agent any 
    
    stages {
        
        stage('Git Pull') {
            steps {
                // credentials are required because its a private repository
                git url: 'https://github.com/nirajlande/Calculator-Using-DevOps.git',
                branch: 'master',
                credentialsId: 'github-pat'
            }
        }
        
        stage('Maven Build + JUnit Tests') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
			steps {
				sh "docker build -t $DOCKERHUB_REGISTRY:latest ."
			}
		}

		stage('Login to Docker Hub') {
			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push Docker Image to Docker Hub') {
			steps {
				sh 'docker push $DOCKERHUB_REGISTRY:latest'
			}
		}
        
        stage('Removing Docker Image from Local') {
            steps {
                sh "docker rmi $DOCKERHUB_REGISTRY:latest"
            }
        }
        
        // Ansible Deploy to remote server (managed host)
        stage('Ansible Deploy') {
            steps {
                ansiblePlaybook becomeUser: 'null',
                colorized: true,
                installation: 'Ansible',
                inventory: 'inventory',
                playbook: 'deploy-playbook.yml',
                sudoUser: 'null'
            }
        }
    }
}
