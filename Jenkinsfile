pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("Pull SRC") {
            steps {
                git branch: 'main', url: 'https://github.com/nisargaraj123/docker-ansible-terraform-project.git'
            }
        }
        stage("move the target") {
            steps {
                sh 'mv target/terraform.war .'
            }
        }
        stage("Prepare Build") {
            steps {
                sh 'mvn clean package'
            }
        }
        stage("build docker image"){
            steps{
                sh 'docker build -t app .'
            }
        }
        stage("tag image"){
            steps{
                sh 'docker tag app nisargaraj/webapp:latest'
            }
        }
        stage("push image"){
            steps{
               sh 'echo "Nisarga@992"| docker login -u nisargaraj --password-stdin'
                sh 'docker push nisargaraj/webapp:latest'
            }
        }
        stage("remove images locally"){
            steps{
                sh 'docker rmi  app'
            }
        }
        
        stage("run ansible playbook") {
            steps {
                sshPublisher(
                    continueOnError: false, 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: "remote",
                            transfers: [
                                sshTransfer(sourceFiles: 'play.yml'),
                                sshTransfer(execCommand: "ansible-playbook play.yml")
                            ],
                            verbose: true
                        )
                    ]
                )
            }
        }
    }
}