pipeline{
    agent{
        any
    }
    tools {
        jdk 'Java17'
        maven 'mvn3'
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
    }
}
