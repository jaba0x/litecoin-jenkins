pipeline {
    agent any
    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
    }
    environment {
        PROJECT = "litecoin"
        IMAGE = "jaba0x/litecoin:0.18.1"
        DEPLOYMENT = "litecoin"
    }
    stages {
        stage('create aws profile') {
            steps {
                sh label: 'aws configure',
                   script: 'aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID} --profile roydemus && aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY} --profile roydemus'
            }
        }
        stage('Before steps') {
            steps {
                sh label: 'authenticate ecr',
                   script: 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 752023216802.dkr.ecr.us-east-1.amazonaws.com'
            }
        }
        stage('Build') {
            steps {
                sh label: 'build container image',
                   script: 'docker build -t 752023216802.dkr.ecr.us-east-1.amazonaws.com/litecoin:latest -f Dockerfile . --no-cache'
            }
        }
        stage('Push') {
            steps {
                sh label: 'push image',
                   script:  'docker push 752023216802.dkr.ecr.us-east-1.amazonaws.com/litecoin:latest'
            }
        }
        stage('Deploy to k8s') {
            environment {
                KUBECONFIG = "/root/.kube/kubeconfig"
            }
            steps {
                sh label: 'deploy',
                   script: '''
                            kubectl apply --kubeconfig=${KUBECONFIG} -f k8s-manifest.yaml
                            kubectl --kubeconfig=${KUBECONFIG} set image deployments/${DEPLOYMENT} ${DEPLOYMENT}=${IMAGE}:${GIT_COMMIT}
                            '''
            }
        }
}
}
