pipeline {
    agent any
    parameters {
        booleanParam(name: 'enforceParams', defaultValue: false,
            description: 'Should some env variables be overridden by parameters (for manually triggered builds)')
        booleanParam(name: 'DeployPROD', defaultValue: false, description: 'Should PROD be deployed with git tag provided?')
        gitParameter branchFilter: 'origin/(release/.*)', defaultValue: 'main', name: 'ReleaseBranch', type: 'PT_BRANCH', sortMode: 'DESCENDING_SMART'
        gitParameter defaultValue: 'main', name: 'TAG', type: 'PT_TAG', sortMode: 'DESCENDING_SMART'
    }
    stages {
        stage('prepare') {
            steps {
                cleanWs()
                checkout([
                    $class: 'GitSCM',
                    branches: scm.branches,
                    doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                    extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: false]],
                    userRemoteConfigs: scm.userRemoteConfigs
                ])
                 script {
                    // Print all branches
                    sh """
                    echo "Listing all branchess (local and remote):"
                    git branch -a
                    echo "Creating the release branch"
                    git checkout -b "release-${TAG}"
                    git push origin "release-${TAG}"
                    """
                    // Fetch the associated tag
                    env.RELATED_TAG = sh(script: "git describe --tags remotes/origin/${params.ReleaseBranch}", returnStdout: true).trim()
                    echo "Associated Tag: ${env.RELATED_TAG}"
                }
            }
        }
    }
}
// def getGitTag(){
//     return node {
//     def tags = ''
//     withCredentials([usernamePassword(credentialsId: 'personal-token', 
//                                       usernameVariable: 'GH_ENTERPRISE_TOKEN_USR',
//                                       passwordVariable: 'GH_ENTERPRISE_TOKEN_PSW')]){
//         tags = sh(script: '''curl -s -L \
//                                 -H "Accept: application/vnd.github+json" \
//                                 -H "Authorization: Bearer ${GH_ENTERPRISE_TOKEN_PSW}" \
//                                 -H "X-GitHub-Api-Version: 2022-11-28" \
//                                 https://api.github.com/repos/Kiran-hub01/Demorepo/releases  \
//                                 | grep '"name":' | awk -F ': "' '{print $2}' | sed 's/",//g' | sort -rV''', returnStdout: true).trim()
//         }
//     def prefix_release = tags.split("\n").collect {tag -> "release-${tag.trim()}"}
//     prefix_release = [""] + prefix_release
//     return prefix_release.join("\n")
//     }
// }
