pipeline{
    
    agent any
    
    tools{
        maven 'maven3.8'
    }
    
    stages{
        
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/BilalHaiderRizvi/devops-automation']])
                bat 'mvn clean install'
            }
        }
        
        stage('Build Docker Image'){
            steps{
                script{
                    bat 'docker build -t haiderbilal/devops-integration .'
                }
            }
        }
        
        stage('Push Image To DockerHub'){
             steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat 'docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'
                }
                bat 'docker push haiderbilal/devops-integration:latest'
        
            }
        }
        stage('Deploy') {
             steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'my-ssh-credentials', keyFileVariable: 'SSH_PRIVATE_KEY', passphraseVariable: '', usernameVariable: 'SSH_USERNAME')]) {
                    powershell """
                        New-Item -ItemType Directory -Path 'C:\\home\\ec2-user'
                        $SSHOptions = '-o StrictHostKeyChecking=no'
                        ssh.exe $SSHOptions -i `"$env:SSH_PRIVATE_KEY`" `"$env:SSH_USERNAME@<EC2-instance-IP-address>`" `"echo 'Hello World' >> /home/ubuntu/hello.txt`"
                    """
                }
            }
        }
    }
}
