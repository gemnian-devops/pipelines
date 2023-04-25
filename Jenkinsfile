pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent'
            defaultContainer 'jnlp'
            yaml """
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                jenkins: agent
            spec:
              containers:
              - name: maven
                image: maven:3.6.1-jdk-11
                command:
                - cat
                tty: true
              - name: docker
                image: docker:latest
                command:
                - cat
                tty: true
              - name: jnlp
                image: jenkins/jnlp-slave:4.10-1-alpine
                tty: true
            """
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-app .'
            }
        }
        stage('Push to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server 'artifactory-server'
                    def rtDocker = Artifactory.docker server: server
                    rtDocker.push 'my-app', 'latest'
                }
            }
        }
    }
}
