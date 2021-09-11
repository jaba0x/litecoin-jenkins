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
        stage('Build') {
            steps {
                sh label: 'build docker image',
                   script: 'docker build . \
                            -f Dockerfile \
                            -t jaba0x/litecoin:${GIT_COMMIT} \
                            -t jaba0x/litecoin:latest'
            }
        }
//         stage('Push') {
//             steps {
//                 sh label: 'push docker image',
//                    script:  '''
//                             docker push jaba0x/litecoin:${GIT_COMMIT}
//                             docker push jaba0x/litecoin:latest
//                             '''
//             }
//         }
//         stage('Deploy to k8s') {
//             environment {
//                 KUBECONFIG = "/root/.kube/kubeconfig"
//             }
//             steps {
//                 sh label: 'deploy',
//                    script: '''
//                             kubectl apply --kubeconfig=${KUBECONFIG} -f k8s-manifest.yaml
//                             kubectl --kubeconfig=${KUBECONFIG} set image deployments/${DEPLOYMENT} ${DEPLOYMENT}=${IMAGE}:${GIT_COMMIT}
//                             '''
//             }
//         }
}
}
