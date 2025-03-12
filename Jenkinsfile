// Defines that this is a pipeline and can run on any available Jenkins agent
pipeline {
    agent any
    
    // Sets up environment variables
    environment {
        // Creates secure credentials for DockerHub login (stored in Jenkins)
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        // Defines your Docker image name
        DOCKER_IMAGE = "avichaye/docker-demo-app"
        // Sets the version tag
        DOCKER_TAG = "v1.0"
        ANSIBLE_SUDO_PASS = credentials('ansible_sudo_pass')
    }
    
    // Define the pipeline stages
    stages {
        // Stage 1: Builds Docker image from your Dockerfile
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        // Stage 2: Logs into DockerHub using credentials
        stage('Login to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        
        // Stage 3: Pushes the built image to DockerHub
        stage('Push to DockerHub') {
            steps {
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        // Stage 4: Deploys the Docker image to your server using Ansible
        stage('Deploy with Ansible') {
            steps {
                sh """
                    ansible-playbook -i inventory.ini deploy-playbook.yml \
                    -e "docker_image=${DOCKER_IMAGE}" \
                    -e "docker_tag=${DOCKER_TAG}"
                """
            }
        }
    }
    
    // Always runs at the end - logs out of DockerHub
    post {
        always {
            sh 'docker logout'
        }
    }
}