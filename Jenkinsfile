pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('githubPersonalToken')
        ORG_NAME = ''
        REPO_NAME = ''
        BRANCH_NAME = ''
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    ORG_NAME = selectOrganization(GITHUB_TOKEN)
                    REPO_NAME = selectRepo(GITHUB_TOKEN, ORG_NAME)
                    BRANCH_NAME = selectBranch(GITHUB_TOKEN, ORG_NAME, REPO_NAME)
                }
            }
        }

        stage('Checkout and Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: BRANCH_NAME]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/${ORG_NAME}/${REPO_NAME}.git"]]])
                // Your build steps go here, e.g.
                // sh './gradlew build'
            }
        }
    }
}

def selectOrganization(githubToken) {
    def correctOrg = false
    def chosenOrg
    while(!correctOrg) {
        def apiUrl = "https://api.github.com/user/orgs"
        def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()
        
        def orgs = readJSON text: response
        orgs = orgs.findAll { it.login.contains('dev') }
        
        def choices = orgs.collect { org -> "${org.login}" }
        chosenOrg = input(message: 'Please choose an organization:', parameters: [choice(name: 'ORG_CHOICE', choices: choices, description: 'Pick your organization')])
        
        correctOrg = input(id: 'confirm', message: "You chose '${chosenOrg}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'yesNo')])
    }
    return chosenOrg
}
def selectRepo(githubToken, orgName) {
    def correctRepo = false
    def chosenRepo
    while(!correctRepo) {
        def apiUrl = "https://api.github.com/orgs/${orgName}/repos"
        def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()
        
        def repos = readJSON text: response
        repos = repos.findAll { it.name.contains('backend') } // If you still need filtering.
        
        def choices = repos.collect { repo -> "${repo.name}" }
        chosenRepo = input(message: 'Please choose a repo:', parameters: [choice(name: 'REPO_CHOICE', choices: choices, description: 'Pick your repo')])
        
        correctRepo = input(id: 'confirm', message: "You chose '${chosenRepo}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'yesNo')])
    }
    return chosenRepo
}

def selectBranch(githubToken, orgName, repo) {
    def correctBranch = false
    def chosenBranch
    while(!correctBranch) {
        def apiUrl = "https://api.github.com/repos/${orgName}/${repo}/branches"
        def response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()
        
        def branches = readJSON text: response
        branches = branches.findAll { it.name == 'master' || it.name == 'develop' || it.name.startsWith('feature-') }
        
        def choices = branches.collect { branch -> "${branch.name}" }
        chosenBranch = input(message: 'Please choose a branch:', parameters: [choice(name: 'BRANCH_CHOICE', choices: choices, description: 'Pick your branch')])
        
        correctBranch = input(id: 'confirm', message: "You chose '${chosenBranch}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'yesNo')])
    }
    return chosenBranch
}




