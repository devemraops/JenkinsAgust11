pipeline {
    agent any

    environment {
        ORG_NAME = ''
        REPO_NAME = ''
        BRANCH = ''
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'githubPersonalToken', variable: 'GITHUB_TOKEN')]) {
                        env.ORG_NAME = selectOrganization(GITHUB_TOKEN)
                        env.REPO_NAME = selectRepo(GITHUB_TOKEN, env.ORG_NAME)
                        env.BRANCH = selectBranch(GITHUB_TOKEN, env.ORG_NAME, env.REPO_NAME)
                    }
                }
            }
        }

        stage('Checkout and Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: env.BRANCH]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/${env.ORG_NAME}/${env.REPO_NAME}.git"]]])
                
                // Your build steps go here, e.g.
                // sh './gradlew build'
            }
        }
    }
}

def selectOrganization(githubToken) {
    def apiUrl = "https://api.github.com/user/orgs"
    def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()

    def orgs = readJSON text: response

    def choices = orgs.collect { org -> "${org.login}" }
    return input(message: 'Please choose an organization:', parameters: [choice(name: 'ORG_CHOICE', choices: choices, description: 'Pick your organization')])
}

def selectRepo(githubToken, orgName) {
    def apiUrl = "https://api.github.com/orgs/${orgName}/repos"
    def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()

    def repos = readJSON text: response

    def choices = repos.collect { repo -> "${repo.name}" }
    return input(message: 'Please choose a repo:', parameters: [choice(name: 'REPO_CHOICE', choices: choices, description: 'Pick your repo')])
}

def selectBranch(githubToken, orgName, repo) {
    def apiUrl = "https://api.github.com/repos/${orgName}/${repo}/branches"
    def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()

    def branches = readJSON text: response

    def choices = branches.collect { branch -> "${branch.name}" }
    return input(message: 'Please choose a branch:', parameters: [choice(name: 'BRANCH_CHOICE', choices: choices, description: 'Pick your branch')])
}



