def branch = "main"
def repo = "https://github.com/MuhSatriyo/retail-store-sample-app.git"
def cred = "ssh_agent"
def dir = "~/retail-store-sample-app"
def location = "~/retail-store-sample-app/dist/docker-compose"
def server = "satriyo@27.112.78.8"
def dockerusername = "muhsatriyo"
def imageConfigs = [
    [imagename: "mariadb"],
    [imagename: "rabbitmq"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-assets"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-checkout"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-orders"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-cart"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-catalog"],
    [imagename: "public.ecr.aws/aws-containers/retail-store-sample-ui"],
    [imagename: "amazon/dynamodb-local"]
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
                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                             docker rmi ${imagename} || true
                             exit
                        EOF
                        """
                    }
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    sshagent([cred]) {
                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                             cd ${location}
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

