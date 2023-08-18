pipeline {
    agent any
    
    stages { 
        stage('Deploy using SSH Key') {
            steps {
                // Use the sshagent step to load and use the SSH private key
                sshagent(credentials: ['devops43']) {
                    // Run SSH commands securely here
                    sh '''
                    ssh devops@192.168.70.43 "cd /home/devops/deployment && cd .. && rm -rf deployment/* && rm -rf deployment/.*"
                    scp -r . devops@192.168.70.43:/home/devops/deployment
                    ssh devops@192.168.70.43 "cd /home/devops/deployment/ && docker-compose up -d"
                    '''
                }
            }
        }
    }
}
