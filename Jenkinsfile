pipeline {
    agent any
    
    stages { 
        stage('Deploy using SSH Key') {
            steps {
                // Use the sshagent step to load and use the SSH private key
                sshagent(credentials: ['devops43']) {
                    // Run SSH commands securely here
                    sh '''
                    scp -r ssl devops@192.168.70.43:/home/devops/deployment/
                    scp docker-compose.yml docker-compose-2.yml nginx.conf .env .dockerignore devops@192.168.70.43:/home/devops/deployment/
                    ssh devops@192.168.70.43 "sh -c 'if [ ! -d "/home/devops/deployment/mysql_db" ]; then mkdir -p "/home/devops/deployment/mysql_db"; fi'
                    ssh devops@192.168.70.43 "cd /home/devops/deployment/ && docker-compose -f docker-compose-2.yml down && docker-compose up -f docker-compose-2.yml -d"
                    '''
                }
            }
        }
    }
}
