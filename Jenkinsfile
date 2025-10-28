pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'komalgt/app:latest'
        INVENTORY = 'ansible/inventory'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
        stage('Deploy with Ansible') {
            steps {
                sh '''
                    cd ansible
                    ansible-playbook -i inventory playbook.yml --extra-vars "docker_image=${DOCKER_IMAGE}"
                '''
            }
        }
    }
}
