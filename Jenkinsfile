pipeline {
 agent any

 options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timeout(time: 1, unit: 'HOURS')
    }

parameters {
    string(name: 'RELEASE', defaultValue: 'dev', description: 'RELEASE')
    string(name: 'EMAIL_RECIPIENTS', defaultValue: 'tejinder.singh@inmindcloud.com', description: 'EMAIL_RECIPIENTS')
    string(name: 'AWS_REGION', defaultValue: '', description: 'AWS_REGION')
    string(name: 'AWS_EB_APP', defaultValue: '', description: 'AWS_EB_APP')
    string(name: 'ISS_WAR_FILE_NAME', defaultValue: '', description: 'ISS_WAR_FILE_NAME')
    string(name: 'AWS_S3_BUCKET', defaultValue: '', description: 'AWS_S3_BUCKET')
    string(name: 'AWS_APP_ENV', defaultValue: '', description: 'AWS_APP_ENV')
}

stages {
    stage('Deploy-Release') {
        //copy war files from "build-webapp-${RELEASE} to "target" folder"
        copyArtifacts projectName: "build-webapp-${RELEASE}", target: 'target', fingerprintArtifacts: true, selector: lastSuccessful()

        //rename war file
        sh """
        echo 'Hello'
        mv target/com.firstmavenproject/webapp/0.0.1-SNAPSHOT/webapp-0.0.1-SNAPSHOT.war target/com.firstmavenproject/webapp/0.0.1-SNAPSHOT/${ISS_WAR_FILE_NAME}.war
        """
    }
}


}