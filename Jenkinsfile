pipeline {
    agent any
    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
    }
    environment {
        PROJECT = "litecoin"
        IMAGE = "752023216802.dkr.ecr.us-east-1.amazonaws.com/litecoin:latest"
        DEPLOYMENT = "litecoin"
        KUBECONFIG = "~/.kube/kubeconfig"
        K8S_CLUSTER_NAME = "test-cluster"
        AWS_PROFILE_NAME = "roydemus"
    }
    stages {
        stage('create aws profile') {
            steps {
                sh label: 'setup aws profile',
                   script: 'aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID} --profile ${AWS_PROFILE_NAME} && aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY} --profile ${AWS_PROFILE_NAME} && aws eks --region us-east-1 update-kubeconfig --name ${K8S_CLUSTER_NAME} --profile ${AWS_PROFILE_NAME}}'
            }
        }
        stage('ecr login') {
            steps {
                sh label: 'authenticate ecr',
                   script: 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 752023216802.dkr.ecr.us-east-1.amazonaws.com'
            }
        }
        stage('Build') {
            steps {
                sh label: 'build container image',
                    script: 'docker build -t ${IMAGE} -f Dockerfile --no-cache .'
            }
        }
        stage('Push') {
            steps {
                sh label: 'push image',
                    script:  'docker push ${IMAGE}'
            }
        }
        stage('Deploy to k8s') {
            steps {
                sh label: 'deploy',
                   script: 'kubectl apply -f k8s-manifest.yaml && kubectl --kubeconfig=${KUBECONFIG} set image deployments/${DEPLOYMENT} ${DEPLOYMENT}=${IMAGE}'
            }
        }
}
}