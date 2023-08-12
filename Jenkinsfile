pipeline 
{ 
    agent any parameters
    { 
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name: 'BRANCH', type: 'PT_BRANCH' 
    }
    stages 
    { 
       stage("list all branches") 
       { 
           steps 
           { 
                git branch: "${params.BRANCH}", credentialsId: "SSH_user_name_with_private_key", url: "ssh://git@myCompanyBitBucketSite.com:port/myRepository.git" 
           } 
      } 
   } 
}
