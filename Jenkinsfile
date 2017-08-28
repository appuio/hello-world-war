pipeline {
    agent { node 'maven' }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
}

stages {
    stage('Build') {
        steps {
                checkout scm
                sh 'mvn -B -V -U -e clean verify -Dsurefire.useFile=false'
                archiveArtifacts 'target/*.?ar'
        }
        post {
            always {
                junit 'target/**/*.xml'  // Requires JUnit plugin
            }
        }
    }
}
