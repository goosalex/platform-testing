node {
    try {
        stage 'Build'
        def workspace = pwd() 

        sh '''
            echo $PWD
            echo $BRANCH_NAME
            cd $PWD@script/;
            mvn versions:set -DnewVersion=$BRANCH_NAME
            mvn clean package
            tar uf target/platform-testing-cdh-$BRANCH_NAME.jar 
            tar uf target/platform-testing-general-$BRANCH_NAME.jar 
        '''

        stage 'Test'
        sh '''
        '''

        stage 'Deploy' 

        build job: 'deploy-component', parameters: [[$class: 'StringParameterValue', name: 'branch', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'component', value: "platform-testing"],[$class: 'StringParameterValue', name: 'release_path', value: "platform/releases"],[$class: 'StringParameterValue', name: 'release', value: "${workspace}@script/target/platform-testing-cdh-${env.BRANCH_NAME}.tar.gz"]]

        build job: 'deploy-component', parameters: [[$class: 'StringParameterValue', name: 'branch', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'component', value: "platform-testing"],[$class: 'StringParameterValue', name: 'release_path', value: "platform/releases"],[$class: 'StringParameterValue', name: 'release', value: "${workspace}@script/target/platform-testing-general-${env.BRANCH_NAME}.tar.gz"]]
    
        emailext attachLog: true, body: "Build succeeded (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} succeeded", to: "${env.EMAIL_RECIPIENTS}"
    }
    catch(error) {
        emailext attachLog: true, body: "Build failed (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} failed", to: "${env.EMAIL_RECIPIENTS}"
        currentBuild.result = "FAILED"
        throw error
    }

}