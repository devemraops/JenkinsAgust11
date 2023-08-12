pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('githubPersonalToken')
        ORG_NAME = ''
        REPO_NAME = ''
        REF_NAME = ''  // This can be either branch or tag
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    ORG_NAME = selectOrganization(GITHUB_TOKEN)
                    REPO_NAME = selectRepo(GITHUB_TOKEN, ORG_NAME)
                    REF_NAME = selectBranchOrTag(GITHUB_TOKEN, ORG_NAME, REPO_NAME)
                }
            }
        }

        stage('Checkout and Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: REF_NAME]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: "https://github.com/${ORG_NAME}/${REPO_NAME}.git"]]])
                // Your build steps go here, e.g.
                // sh './gradlew build'
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
        def response
        try {
            response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()
            def orgs = readJSON text: response
            
            // Get a prefix or starting word from the user
            def prefix = input(message: 'What word does your organization start with?', parameters: [string(name: 'ORG_START_WORD', defaultValue: '', description: 'Enter the starting word or prefix of your organization.')])
            
            if (prefix == '') {
                error("You must provide a prefix for the organization name to proceed.")
            }
            
            // Filter organizations based on the prefix
            def filteredOrgs = orgs.findAll { org -> org.login.toLowerCase().startsWith(prefix.toLowerCase()) }
            
            if (filteredOrgs.isEmpty()) {
                error("No organizations found starting with '${prefix}'. Please try again.")
            }
            
            def choices = filteredOrgs.collect { org -> "${org.login}" }
            chosenOrg = input(message: 'Please choose an organization:', parameters: [choice(name: 'ORG_CHOICE', choices: choices, description: 'Pick your organization')])
        } catch(Exception e) {
            error("Failed to fetch organizations: ${e.message}")
        }
        
        correctOrg = input(id: 'confirm', message: "You chose '${chosenOrg}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'Yes\tNo')])
    }
    return chosenOrg
}
def selectRepo(githubToken, orgName) {
    def correctRepo = false
    def chosenRepo
    while(!correctRepo) {
        def apiUrl = "https://api.github.com/orgs/${orgName}/repos"
        def response
        try {
            response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${apiUrl}'", returnStdout: true).trim()
            def repos = readJSON text: response
            
            // Get a prefix or starting word from the user
            def prefix = input(message: 'What word does your repo start with?', parameters: [string(name: 'REPO_START_WORD', defaultValue: '', description: 'Enter the starting word or prefix of your repo.')])
            
            // Filter repositories based on the prefix
            def filteredRepos = repos.findAll { repo -> repo.name.toLowerCase().startsWith(prefix.toLowerCase()) }
            
            if (filteredRepos.isEmpty()) {
                error("No repos found starting with '${prefix}'. Please try again.")
            }
            
            def choices = filteredRepos.collect { repo -> "${repo.name}" }
            chosenRepo = input(message: 'Please choose a repo:', parameters: [choice(name: 'REPO_CHOICE', choices: choices, description: 'Pick your repo')])
        } catch(Exception e) {
            error("Failed to fetch repositories: ${e.message}")
        }
        
        correctRepo = input(id: 'confirm', message: "You chose '${chosenRepo}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'Yes\tNo')])
    }
    return chosenRepo
}

def selectBranchOrTag(githubToken, orgName, repo) {
    def correctRef = false
    def chosenRef
    while(!correctRef) {
        def branchApiUrl = "https://api.github.com/repos/${orgName}/${repo}/branches"
        def tagApiUrl = "https://api.github.com/repos/${orgName}/${repo}/tags"
        def response
        try {
            // For branches
            response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${branchApiUrl}'", returnStdout: true).trim()
            def branches = readJSON text: response
            
            // For tags
            response = sh(script: "curl -s -H 'Authorization: token ${githubToken}' '${tagApiUrl}'", returnStdout: true).trim()
            def tags = readJSON text: response
            
            // Get a prefix or starting word from the user for branch or tag
            def prefix = input(message: 'What word does your branch or tag start with?', parameters: [string(name: 'REF_START_WORD', defaultValue: '', description: 'Enter the starting word or prefix of your branch or tag.')])
            
            // Filter branches and tags based on the prefix
            def filteredBranches = branches.findAll { branch -> branch.name.toLowerCase().startsWith(prefix.toLowerCase()) }
            def filteredTags = tags.findAll { tag -> tag.name.toLowerCase().startsWith(prefix.toLowerCase()) }

            if (filteredBranches.isEmpty() && filteredTags.isEmpty()) {
                error("No branches or tags found starting with '${prefix}'. Please try again.")
            }
            
            def choices = filteredBranches.collect { branch -> "branch: ${branch.name}" } + filteredTags.collect { tag -> "tag: ${tag.name}" }
            chosenRef = input(message: 'Please choose a branch or tag:', parameters: [choice(name: 'REF_CHOICE', choices: choices, description: 'Pick your reference')])
        } catch(Exception e) {
            error("Failed to fetch branches or tags: ${e.message}")
        }

        correctRef = input(id: 'confirm', message: "You chose '${chosenRef}'. Is this correct?", parameters: [booleanParam(defaultValue: true, description: 'Confirm selection', name: 'Yes\tNo')])
    }
    return chosenRef.split(": ")[1]  // This will extract the actual ref name after "branch: " or "tag: "
}