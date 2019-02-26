pipeline {
    agent {
        label "master"
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '50'))
        timestamps()
        ansiColor('xterm')
        parallelsAlwaysFailFast()
    }
    stages {
        stage("Prepare") {
            steps {
                script {
                    branchName = sh(
                        script: "git rev-parse --abbrev-ref HEAD",
                        returnStdout: true
                    ).trim()
                }
                stash includes: "**/*", name: "sources"
            }
        }
        stage("Build") {
            agent {
                label "maven-slave"
            }
            steps {
                unstash name: "sources"
                sh "mvn clean install"
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
