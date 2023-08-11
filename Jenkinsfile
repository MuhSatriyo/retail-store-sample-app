def branch = "main"
def repo = "https://github.com/MuhSatriyo/retail-store-sample-app.git"
def cred = "ssh_agent"
def dir = "~/retail-store-sample-app"
def server = "satriyo@27.112.78.8"
def dockerusername = "muhsatriyo"
def containerConfigs = [
    "docker-compose-rabbitmq-1",
    "docker-compose-catalog-db-1",
    "docker-compose-catalog-1",
    "docker-compose-orders-db-1",
    "docker-compose-checkout-redis-1",
    "docker-compose-orders-1",
    "docker-compose-ui-1",
    "docker-compose-assets-1",
    "docker-compose-carts-1",
    "docker-compose-checkout-1",
    "docker-compose-carts-db-1"
]
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
                        for (containerName in containerConfigs) {
                            sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                docker stop ${containerName} -f || true
                                docker rm ${containerName} -f || true
                                exit
                            EOF
                            """
                        }
                    }
                }
            }
        }
        stage('Delete Images Before') {
            steps {
                script {
                    sshagent([cred]) {
                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                             cd ${dir}
                             docker rmi ${imageConfigs} || true
                             exit
                        EOF
                        """
                    }
                }
            }
        }
        stage('Build Docker Images and running on container') {
            steps {
                script {
                    sshagent([cred]) {
                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                             cd ${dir}/dist/docker-compose
                             MYSQL_PASSWORD='12345678' docker compose up -d || true
                             exit
                        EOF
                        """
                        }
                    }
               }
          }
     }
}
