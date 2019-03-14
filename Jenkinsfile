pipeline {
    agent {
        label "master"
    }
    parameters {
        choice(name: 'action', choices: 'check_and_deploy\npromote')
        choice(name: 'environment', choices: 'dev\nqa\nprod')
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
                stash includes: "**/*", name: "sources"
            }
        }
        stage("Build") {
            when {
              anyOf {
                allOf {
                    environment name: 'environment', value: 'dev'
                    environment name: 'action', value: 'check_and_deploy'
                }
                branch 'feature/*'
              }
            }
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
        stage("Dockerize") {
            when {
              anyOf {
                allOf {
                    environment name: 'environment', value: 'dev'
                    environment name: 'action', value: 'check_and_deploy'
                }
                branch 'feature/*'
              }
            }
            steps {
                sh "true"
            }
        }
        stage("Publish") {
            when {
                allOf {
                    environment name: 'environment', value: 'dev'
                    environment name: 'action', value: 'check_and_deploy'
                }
            }
            steps {
                sh "true"
            }
        }
        stage("Promote") {
            when {
                environment name: 'action', value: 'promote'
            }
            steps {
                sh "true"
            }
        }
        stage("Deploy") {
            when {
                anyOf {
                    allOf {
                        environment name: 'action', value: 'promote'
                    }
                    environment name: 'environment', value: 'dev'
                }
            }
            steps {
                sh "true"
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}