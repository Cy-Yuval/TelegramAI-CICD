pipeline {
    agent {
        docker {
            image '700935310038.dkr.ecr.us-east-1.amazonaws.com/yuval-jenkins-exe-agent-image:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
            registryUrl 'https://700935310038.dkr.ecr.us-east-1.amazonaws.com'
            registryCredentialsId 'ecr:us-east-1:aws'
        }
    }

    environment {
        APP_ENV = "prod"
        YAML_MANIFEST_PATH = "infra/k8s/worker.yaml"
    }

    parameters {
        string(name: 'WORKER_IMAGE_NAME')
    }

    stages {
        stage('Create Worker Manifest') {
            steps {
                sh '''
                sed -i "s|IMAGE_PLACEHOLDER|$WORKER_IMAGE_NAME|g" $YAML_MANIFEST_PATH
                sed -i "s|ENV_PLACEHOLDER|$APP_ENV|g" $YAML_MANIFEST_PATH
                '''
            }
        }

        stage('Worker Deploy') {
            steps {
                withCredentials([
                    file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')
                ]) {
                    sh '''
                    # apply the configurations to k8s cluster
                    kubectl apply --kubeconfig $KUBECONFIG -f $YAML_MANIFEST_PATH -n $APP_ENV
                    '''
                }
            }
        }
    }
}