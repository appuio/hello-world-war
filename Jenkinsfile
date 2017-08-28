pipeline {
    agent { label 'maven' }
    environment {
        APP_NAME = "hello-world"
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    triggers {
        pollSCM('H/5 * * * *')
    }

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh 'mvn -B -V -U -e clean verify -Dsurefire.useFile=false'
                //archiveArtifacts 'target/*.?ar'
            }
        }

         // trigger Build on OpenShift
        stage('BuildOnOpenShift') {
            steps {
                sh "rm -rf work"
                sh "mkdir -p work/deployments"
                sh "mv target/*.?ar work/deployments"

                sh "oc start-build ${env.APP_NAME} --from-dir=work --follow --wait -n ${env.PROJECT_NAME}"
                //openshiftBuild(buildConfig: appName, showBuildLogs: 'true')
            }
        }

        /*stage('Deploy') {
            openshiftDeploy(deploymentConfig: env.APP_NAME)
        }*/
    }
}
