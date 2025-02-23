pipeline {
    agent any
    environment {
        SERVER_IP = '192.168.0.39'
        DEPLOY_DIR = '/opt/stacks/pi-hole'
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
                    // Copy docker-compose.yml
                    sh "scp -o StrictHostKeyChecking=no docker-compose.yml ${SERVER_IP}:${DEPLOY_DIR}/docker-compose.yml"

                    // Copy tailscale https config
                    sh "scp -o StrictHostKeyChecking=no pihole.json ${SERVER_IP}:${DEPLOY_DIR}/pihole.json"

                    // Copy custom resolv.conf
                    sh "scp -o StrictHostKeyChecking=no resolv.conf ${SERVER_IP}:${DEPLOY_DIR}/resolv.conf"
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
                        docker compose pull && \
                        docker compose up -d"
                    """
                }
            }
        }
    }
}
