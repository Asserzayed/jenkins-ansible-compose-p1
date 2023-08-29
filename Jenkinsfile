pipeline {
    agent any
    
    stages { 
        stage('Deploy using SSH Key') {
            steps {
                // Use the sshagent step to load and use the SSH private key
                sshagent(credentials: ['devops43']) {
                    // Run SSH commands securely here
                    sh '''
                    scp -r . devops@192.168.70.43:/home/devops/deployment/interns
                    ssh devops@192.168.70.43 "cd /home/devops/deployment/interns && docker-compose -f docker-compose-2.yml down && docker-compose up -f docker-compose-2.yml -d"
                    '''
                }
            }
        }
    }
}
