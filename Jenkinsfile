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
                        git config --local --unset credential.helper || true
                        git config --global --unset credential.helper || true
                        git config --system --unset credential.helper || true
                        git config --global credential.helper store
                        git remote set-url origin git@github.com:Kiran-hub01/Demorepo.git
                        echo "config list"
                        git config --list
                        git remote -v
                        echo "Listing all branchess (local and remote):"
                        git branch -a
                        git remote -v
                        set -x
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
