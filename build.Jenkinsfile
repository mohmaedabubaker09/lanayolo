def gitCredentials

withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
    gitCredentials = "${env.GIT_USERNAME}:${env.GIT_PASSWORD}@github.com"
}


pipeline {
    agent any

    environment {
        ECR_REGISTRY = "933060838752.dkr.ecr.eu-west-2.amazonaws.com"
        TIMESTAMP = new Date().format('yyyyMMdd_HHmmss')
        IMAGE_TAG = "${env.BUILD_NUMBER}_${TIMESTAMP}"
        ECR_REGION = "eu-west-2"
        AWS_CREDENTIALS_ID = 'AWS credentials'
        KUBE_CONFIG_CRED = 'KUBE_CONFIG_CRED'
        CLUSTER_NAME = "k8s-main"
        CLUSTER_REGION = "us-east-1"
    }

    stages {
        stage('Login to AWS ECR') {
            steps {
                script {
                    withCredentials([aws(credentialsId: AWS_CREDENTIALS_ID, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        // sh 'aws ecr get-login-password --region ${ECR_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}'
                    }
                }
            }
        }

        stage('Build and Push') {
            steps {
                script {
                    echo "IMAGE_TAG: ${IMAGE_TAG}"
                    dockerImage = docker.build("${ECR_REGISTRY}/lana_yolo5_container:${IMAGE_TAG}") // , "--no-cache .")
                    dockerImage.push()
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([aws(credentialsId: AWS_CREDENTIALS_ID, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        // sh 'aws eks update-kubeconfig --region ${CLUSTER_REGION} --name ${CLUSTER_NAME}'
                        withCredentials([file(credentialsId: 'KUBE_CONFIG_CRED', variable: 'KUBECONFIG')]) {
                            sh "sed -i 's|image: .*|image: ${ECR_REGISTRY}/lana_yolo5_container:${IMAGE_TAG}|' lanayolo5-deployment.yaml"
//                             sh 'kubectl apply -f lanayolo5-deployment.yaml' //--validate=false'
                        }
                    }
                }
            }
        }

        stage('Copy File to Agent') {
            steps {
                stash(name: 'yamlFile', includes: 'lanayolo5-deployment.yaml')
            }
        }

        stage('Deploy on Agent') {

            agent {
                label 'K8s_repo_agent'
            }

            steps {
                unstash 'yamlFile'
                echo "Git Credentials: ${gitCredentials}"

                script {
                    def directory = 'lanabot-k8s'
                    if (fileExists(directory)) {
                        sh "rm -rf ${directory}"
                    } else {
                        echo "Directory ${directory} does not exist. Skipping removal."
                    }
                }

                sh "git clone https://${gitCredentials}/mohmaedabubaker09/lanabot-k8s.git"
                sh "cp lanayolo5-deployment.yaml lanabot-k8s/"
                dir("lanabot-k8s") {
                    sh "git add ."
                    sh "git config user.email 'mohmaedabubaker09@gmail.com'"
                    sh "git config user.name 'mohmaedabubaker09'"
                    sh "git commit -m 'Add lana-bot-deployment.yaml'"
                    sh "git push origin main"
                }
            }
        }
    }

    post {
        always {
            sh 'docker rmi $(docker images -q) -f || true'
        }
    }
}
