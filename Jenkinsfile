pipeline {
    agent any
    parameters {
        booleanParam(name: 'DeployPROD', defaultValue: false, description: 'Should PROD be deployed with git tag provided?')
        booleanParam(name: 'Branch', defaultValue: false, description: 'Enable this to deploy through release branch')
        gitParameter branchFilter: 'origin/(release-.*)', defaultValue: 'main', name: 'ReleaseBranch', type: 'PT_BRANCH', sortMode: 'DESCENDING_SMART'
        gitParameter defaultValue: 'main', name: 'TAG', type: 'PT_TAG', sortMode: 'DESCENDING_SMART'
    }
    environment {
        CUSTOMRELEASEBRANCHNAME = "release-${params.TAG}"
        git_creds = credentials('Kiran-hub01')
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
                    userRemoteConfigs: [[
                        url: 'https://github.com/Kiran-hub01/Demorepo.git',
                        credentialsID: 'Kiran-hub01'
                        ]]
                ])
                 script {
                     if (params.Branch) {
                         echo "${params.ReleaseBranch}"
                         env.FINALRELEASEBRANCH = "${params.ReleaseBranch}"
                     } else {
                         sh """
                        echo "Creating the release branch"
                        git checkout -b "${env.CUSTOMRELEASEBRANCHNAME}" "${params.TAG}"
                        git push origin "${env.CUSTOMRELEASEBRANCHNAME}"
                        """
                        env.FINALRELEASEBRANCH = "${env.CUSTOMRELEASEBRANCHNAME}"
                     }
                    
                    // Fetch the associated tag
                    env.RELATED_TAG = sh(script: "git describe --tags remotes/origin/${env.FINALRELEASEBRANCH}", returnStdout: true).trim()
                    echo "Associated Tag: ${env.RELATED_TAG}"
                }
            }
        }
    }
}
