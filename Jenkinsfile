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
            options {
                skipDefaultCheckout true
            }
            steps {
                unstash name: "sources"
                configFileProvider([configFile(fileId: '6bbd8f57-047c-47ea-8255-3e69733a2508', variable: 'MAVEN_SETTINGS_XML')]) {
                    sh 'mvn clean install'
                    sh 'mvn -s $MAVEN_SETTINGS_XML sonar:sonar'
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
