pipeline {
    agent {
        docker {
            image '700935310038.dkr.ecr.us-east-1.amazonaws.com/yuval-jenkins-exe-agent-image:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
            registryUrl 'https://700935310038.dkr.ecr.us-east-1.amazonaws.com'
            registryCredentialsId 'ecr:us-east-1:aws'
        }
    }

    options{
        timestamps()
    }

    environment {
        ECR_REPO = "700935310038.dkr.ecr.us-east-1.amazonaws.com"
        IMAGE_NAME = "yuval_tele_bot"
        IMAGE_TAG = "$BUILD_NUMBER"
        DOCKER_FILE_PATH = "bot/Dockerfile"
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "building..."
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_REPO
                docker build -t $IMAGE_NAME -f $DOCKER_FILE_PATH .
                docker tag $IMAGE_NAME $ECR_REPO/$IMAGE_NAME:$BUILD_NUMBER
                docker push $ECR_REPO/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
            post {
               always {
                    sh 'docker image prune -a --filter "until=240h" --force'
                }
            }
        }
    }
}