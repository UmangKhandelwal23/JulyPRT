pipeline {
    agent {
        label 'Slave01'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker')
        IMAGE_NAME = "umangkhandelwal/jultnewprt:v1"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/UmangKhandelwal23/JulyRepo'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                docker push ${IMAGE_NAME}
                docker logout
                """
            }
        }

        stage('Deploy Application') {
                 agent { label 'built-in' }

             steps {
                checkout scm

             sh '''
                ansible-playbook -i ansible/inventory.ini ansible/site.yml
               '''
             }
         }

        stage('Verify Deployment') {
            agent { label 'built-in' }

            steps {
                sh '''
                ansible App -i ansible/inventory.ini -m shell -a "docker ps"
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀 Employee Portal is live"
        }
        failure {
            echo "Pipeline Failed ❌ Check logs"
        }
    }
}
