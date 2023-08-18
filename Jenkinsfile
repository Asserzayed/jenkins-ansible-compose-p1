pipeline {
    agent any
    
    stages { 
        stage('Deploy using SSH Key') {
            steps {
                // Use the sshagent step to load and use the SSH private key
                sshagent(credentials: ['devops43']) {
                    // Run SSH commands securely here
                    sh '''
                    ssh root@192.168.70.43 "cd /home/devops && rm -rf deployment/*"
                    scp -r . root@192.168.70.43:/home/devops/deployment
                    ssh root@192.168.70.43 "cd /home/devops/deployment/ && rm -rf mysql_db/data/* && docker-compose down && docker-compose up -d"
                    '''
                }
            }
        }
    }
}
