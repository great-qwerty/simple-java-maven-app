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
                withSonarQubeEnv('default') {
                    sh 'mvn clean install sonar:sonar'
                }
                sleep(10)
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
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