pipeline{
    agent{
        node{
            label 'master'
            customWorkspace '/mnt/git-project'
        }
    }
    stages{
        stage ('clone repo'){
            steps{
                sh "rm -rf project*"
                sh "git clone 'https://github.com/bhushuhub/project.git'"
            }
        }
        stage ('create build'){
            steps{
                dir ('/mnt/git-project/project'){
                    sh "mvn clean install"
                }
            }
        }
        stage ('copy .war on slaves'){
            steps{
                sh "chmod -R 777 /mnt/git-project/project/target/LoginWebApp.war"
                sh "scp -i '/mnt/new-keypair.pem' /mnt/git-project/project/target/LoginWebApp.war ec2-user@172.31.91.102:/mnt"
                sh "scp -i '/mnt/new-keypair.pem' /mnt/git-project/project/target/LoginWebApp.war ec2-user@172.31.85.200:/mnt"
            }
        }
        stage ('copy app slave-1'){
            agent{
                node{
                    label 'qa'
                    customWorkspace '/mnt'
                }
            }
            steps{
                sh "sudo cp LoginWebApp.war /mnt/docker/file-1"
                sh "sudo cp LoginWebApp.war /mnt/docker/file-2"
            }
        }
        stage ('deploy app using docker-compose on slave-1'){
            agent{
                node{
                    label 'qa'
                    customWorkspace '/mnt/docker'
                }
            }
            steps{
                sh "sudo docker compose up -d"
            }
        }
        stage ('copy app on slave-2'){
            agent{
                node{
                    label 'dev'
                    customWorkspace '/mnt'
                }
            }
            steps{
                sh "sudo cp LoginWebApp.war /mnt/docker/file-1"
                sh "sudo cp LoginWebApp.war /mnt/docker/file-2"
            }
        }
        stage ('deploy app using docker-compose on slave-2'){
            agent{
                node{
                    label 'dev'
                    customWorkspace '/mnt/docker'
                }
            }
            steps{
                sh "sudo docker compose up -d"
            }
        }
    }
}
