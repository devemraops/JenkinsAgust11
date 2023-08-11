pipeline {
    agent any
    tools {
        jdk 'Java17'
        maven 'mvn3'
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                checkout scm()
            }
        }
        stage('Build App') {
            steps {
                script {
                    sh "mvn clean package"
                }
            }
        }
        stage("Test Application") {
            steps {
                script {
                    sh "mvn test package"
                }
            }
        }
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
    }
}
