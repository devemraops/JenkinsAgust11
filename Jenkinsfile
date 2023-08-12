@Library('shared-libraries2') _

properties([
    parameters([
        string(defaultValue: 'DefaultOrganizationName', description: 'GitHub Organization Name', name: 'ORG_NAME')
    ])
])

pipeline {
    agent any

    environment {
        REPO = ''
        BRANCH = ''
    }

    stages {
        stage('Checkout and Build') {
            steps {
                script {
                    env.REPO = chooseRepo(GITHUB_TOKEN, params.ORG_NAME)
                    env.BRANCH = chooseBranch(GITHUB_TOKEN, env.REPO, params.ORG_NAME)

                    checkout([$class: 'GitSCM', branches: [[name: env.BRANCH]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/${params.ORG_NAME}/${env.REPO}.git"]]])
                
                    // Your build steps go here, e.g.
                    // sh './gradlew build'
                }
            }
        }
    }
}

