pipeline {
    agent any
    parameters {
        booleanParam(name: 'enforceParams', defaultValue: false,
            description: 'Should some env variables be overridden by parameters (for manually triggered builds)')
        booleanParam(name: 'DeployPROD', defaultValue: false, description: 'Should PROD be deployed with git tag provided?')
        choice(
            name: 'PRODReleaseVersionToDeploy',
            choices: getGitTag(),
            description: 'Select the Git release version to deploy'
        )

    }
    stages {
        stage('prepare') {
            steps {
                echo "${params.PRODReleaseVersionToDeploy}"
            }
        }
    }
}
def getGitTag(){
    return node {
    def tags = ''
    withCredentials([usernamePassword(credentialsId: 'bvc-sa-gmo-github', 
                                      usernameVariable: 'GH_ENTERPRISE_TOKEN_USR',
                                      passwordVariable: 'GH_ENTERPRISE_TOKEN_PSW')]){
        tags = sh(script: '''curl -s -L \
                                -H "Accept: application/vnd.github+json" \
                                -H "Authorization: Bearer ${GH_ENTERPRISE_TOKEN_PSW}" \
                                -H "X-GitHub-Api-Version: 2022-11-28" \
                                https://api.github.com/repos/Kiran-hub01/Demorepo/releases  \
                                | grep '"name":' | awk -F ': "' '{print $2}' | sed 's/",//g' | sort -rV''', returnStdout: true).trim()
        }
    def prefix_release = tags.split("\n").collect {tag -> "release-${tag.trim()}"}
    prefix_release = [""] + prefix_release
    return prefix_release.join("\n")
    }
}
