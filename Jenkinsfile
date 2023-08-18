pipeline {
    agent any
    
    stages { 
        stage('Deploy using SSH Key') {
            steps {
                // Use the sshagent step to load and use the SSH private key
                sshagent(credentials: ['devops43']) {
                    // Run SSH commands securely here
                    sh '''
                    cd /home/devops/deployment && docker-compose down && cd .. && rm -rf * && rm -rf .*
                    scp -r . devops@192.168.70.43:/home/devops/deployment/
                    cd /home/devops/deployment/ && docker-compose up -d
                    '''
                }
            }
        }
    }
}


