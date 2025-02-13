pipeline {
    agent any
    environment {
        SERVER_IP = '192.168.0.39'
        DEPLOY_DIR = '/opt/stacks/pihole'
        CREDENTIALS_ID = 'home-intranet-server-ssh'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Lanham-Software-James/Home-Intranet-Pi-Hole.git'
            }
        }

        stage('Copy Files to Server') {
            steps {
                sshagent(credentials: [CREDENTIALS_ID]) {
                    sh "scp -o StrictHostKeyChecking=no docker-compose.prd.yml ${SERVER_IP}:${DEPLOY_DIR}/docker-compose.yml"
                }
            }
        }

        stage('Deploy Services') {
            steps {
                sshagent(credentials: [CREDENTIALS_ID]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_IP} \
                        "cd ${DEPLOY_DIR} && \
                        docker compose down && \
                        docker compose up -d"
                    """
                }
            }
        }
    }
}
