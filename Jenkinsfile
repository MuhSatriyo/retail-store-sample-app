def branch = "main"
def repo = "https://github.com/MuhSatriyo/retail-store-sample-app.git"
def cred = "ssh_agent"
def dir = "~/retail-store-sample-app"
def server = "satriyo@27.112.78.8"
def dockerusername = "muhsatriyo"
def imageConfigs = [
    "mariadb",
    "rabbitmq",
    "public.ecr.aws/aws-containers/retail-store-sample-assets",
    "public.ecr.aws/aws-containers/retail-store-sample-checkout",
    "public.ecr.aws/aws-containers/retail-store-sample-orders",
    "public.ecr.aws/aws-containers/retail-store-sample-cart",
    "public.ecr.aws/aws-containers/retail-store-sample-catalog",
    "public.ecr.aws/aws-containers/retail-store-sample-ui",
    "amazon/dynamodb-local"
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
        stage('Delete Images Before') {
            steps {
                script {
                    sshagent([cred]) {
                        for (imageName in imageConfigs) {
                            sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                cd ${dir}
                                docker rmi ${imageName} || true
                                exit
                            EOF
                            """
                        }
                    }
                }
            }
        }
        stage('Delete Docker Container') {
            steps {
                script {
                    sshagent([cred]) {
                        for (imageName in imageConfigs) {
                            sh """
                                ssh ${server} << EOF
                                    docker stop ${imageName} || true
                                    docker rm ${imageName} || true
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
