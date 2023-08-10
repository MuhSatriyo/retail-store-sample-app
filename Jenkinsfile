def branch = "main"
def repo = "https://github.com/MuhSatriyo/retail-store-sample-app.git"
def cred = "ssh_agent"
def dir = "~/retail-store-sample-app"
def server = "satriyo@27.112.78.8"
def dockerusername = "muhsatriyo"
def imageConfigs = [
    "mariadb:10.9",
    "rabbitmq:3-management",
    "public.ecr.aws/aws-containers/retail-store-sample-assets:0.4.0",
    "public.ecr.aws/aws-containers/retail-store-sample-checkout:0.4.0",
    "public.ecr.aws/aws-containers/retail-store-sample-orders:0.4.0",
    "public.ecr.aws/aws-containers/retail-store-sample-cart:0.4.0",
    "public.ecr.aws/aws-containers/retail-store-sample-catalog:0.4.0",
    "public.ecr.aws/aws-containers/retail-store-sample-ui:0.4.0",
    "amazon/dynamodb-local:1.20.0"
]

pipeline {
    agent any
    stages {
        stage('Repo pull') {
            steps {
                script {
                    sshagent(credentials: [cred]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no -T ${server} << EOF
                                git clone ${repo}
                                cd ${dir}
                                git checkout ${branch} || true
                                git pull origin ${branch} || true
                                exit
                            EOF
                        """
                    }
                }
            }
        }
        stage('Delete Docker Resources') {
            steps {
                script {
                    sshagent([cred]) {
                        for (imageName in imageConfigs) {
                            sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                docker stop ${imageName} -f || true
                                docker rm ${imageName} -f || true
                                docker rmi ${imageName} -f || true
                                exit
                            EOF
                            """
                        }
                    }
                }
            }
        }
    }
}





