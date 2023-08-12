@Library('my-shared-library') _

properties([
    parameters([
        string(defaultValue: 'DefaultOrganizationName', description: 'GitHub Organization Name', name: 'ORG_NAME')
        string(defaultValue: 'RepoName', description: 'GitHub Organization Name', name: 'REPO_NAME')
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
                    env.REPO = chooseRepo(github, params.ORG_NAME)
                    env.BRANCH = chooseBranch(github, env.REPO, params.ORG_NAME)

                    checkout([$class: 'GitSCM', branches: [[name: env.BRANCH]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/${params.ORG_NAME}/${env.REPO}.git"]]])
                
                    // Your build steps go here, e.g.
                    // sh './gradlew build'
                }
            }
        }
    }
}

