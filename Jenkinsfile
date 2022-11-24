pipeline {
 agent any

 options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timeout(time: 1, unit: 'HOURS')
    }

environment {
    AWS_CREDENTIALS=credentials('Inmind-EB-Admin')
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
    stage('Build') {
        steps {
            echo 'Build'
            //copy war files from "build-webapp-${RELEASE} to "target" folder"
            copyArtifacts projectName: "build-webapp-${RELEASE}", target: 'target', fingerprintArtifacts: true, selector: lastSuccessful()

            step([$class: 'AWSEBDeploymentBuilder', applicationName: "${AWS_EB_APP}", awsRegion: "${AWS_REGION}", bucketName: "${AWS_S3_BUCKET}", credentialId: "Inmind-EB-Admin", environmentName: "${AWS_APP_ENV}", maxAttempts: 30, rootObject: 'target/com.firstmavenproject/webapp/0.0.1-SNAPSHOT/webapp-0.0.1-SNAPSHOT.war', versionLabelFormat: "webapp-${BUILD_NUMBER}"])
        
        }

    }
}


}