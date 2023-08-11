pipeline {
    agent any
    tools {
        jdk 'Java17'
        maven 'mvn3'
    }
    stages {
        // stage('Cleanup Workspace') {
        //     steps {
        //         checkout scm()
        //     }
        // }
        stage('Build App') {
            steps {
                sh "mvn clean package"
            }
        }
        stage("Test Application") {
            steps {
                sh "mvn test package"
            }
        }
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
    }
}
