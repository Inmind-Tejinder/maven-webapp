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
    string(name: 'AWS_CREDENTIALS_ID', defaultValue: '', description: 'AWS_CREDENTIALS_ID')
}

stages {
    stage('Deploy') {
        steps {
            echo 'Updating environment properties started'

            withCredentials([aws(credentialsId: "${AWS_CREDENTIALS_ID}", accessKeyVariable: 'AWS_KEY', secretKeyVariable: 'AWS_SECRET')]) {
                bat "aws configure set aws_access_key_id ${AWS_KEY}"
                bat "aws configure set aws_secret_access_key ${AWS_SECRET}"
                bat "aws configure set default.region ${AWS_REGION}"

                bat "aws elasticbeanstalk update-environment --application-name=${AWS_EB_APP} --environment-name=${AWS_APP_ENV} --option-settings Namespace=aws:elasticbeanstalk:application:environment,OptionName=PARAM4,Value=Param4Value"

                echo 'Update environment properties in progress'

                bat "aws elasticbeanstalk wait environment-updated --application-name=${AWS_EB_APP} --environment-names=${AWS_APP_ENV}"

                echo 'Update environment properties completed'

                echo 'Copy artifacts started'
                //copy war files from "build-webapp-${RELEASE} to "target" folder"
                copyArtifacts projectName: "build-webapp-${RELEASE}", target: 'target', fingerprintArtifacts: true, selector: lastSuccessful()

                echo 'Copy artifacts finished'

                step([$class: 'AWSEBDeploymentBuilder', applicationName: "${AWS_EB_APP}", awsRegion: "${AWS_REGION}", bucketName: "${AWS_S3_BUCKET}", keyPrefix: '', credentialId: "${AWS_CREDENTIALS_ID}", environmentName: "${AWS_APP_ENV}", maxAttempts: 30, rootObject: 'target/com.firstmavenproject/webapp/0.0.1-SNAPSHOT/webapp-0.0.1-SNAPSHOT.war', versionLabelFormat: "webapp-${BUILD_NUMBER}"])
            }

        }

    }
}


}