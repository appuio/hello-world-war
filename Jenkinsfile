pipeline {
    agent { label 'master' }
    environment {
        NAMESPACE = env.PROJECT_NAME
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
            agent { label 'maven' }
            steps {
                sh "env"
                sh 'mvn -B -V -U -e clean verify -Dsurefire.useFile=false'
                stash name: "binsrc", includes: "target/*.war"
                //archiveArtifacts 'target/*.?ar'
            }
        }

         // trigger Build on OpenShift
        stage('BuildOnOpenShift') {
            steps {
                unstash name: "binsrc"
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
