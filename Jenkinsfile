def gv

pipeline {
    agent any
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    echo "building jar"
                    gv.buildJar()
                }
            }
        }
        stage("build image") {
            steps {
                script {
                    echo "building docker image"
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo "deploying docker image to EC2"
                    def dockerComposeCmd = "docker-compose -f docker-compose.yaml --detach"
                    sshagent(['ec2-server-key']) {
                        sh "scp docker-compose.yaml ec2-user@3.68.230.224:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@3.68.230.224 ${dockerComposeCmd}"
                    }
                }
            }
        }
    }   
}