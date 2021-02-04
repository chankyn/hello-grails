#!/usr/bin/env groovy

pipeline {
    agent any
  
    stages {
        
        stage('Build') {
            steps {
                withGradle {
                    sh './gradlew assemble'
                }
            }
        }
        stage('Test') {
            steps {
                withGradle {
                    sh './gradlew clean test'
                }
            }
            post {
                always {
                    junit 'build/test-results/test/TEST-*.xml'
                }
            }
        }
        stage('iTest') {
            steps {
                configFileProvider([configFile(fileId: 'hello-grails-gradle.properties', targetLocation: 'gradle.properties')]) {
                    withGradle {
                        sh './gradlew iT'
                    }
                }
            }
            post {
                always {
                    junit 'build/test-results/integrationTest/TEST-*.xml'
                }
            }
        }
        stage('codenarc') {
            steps {
                withGradle {
                    sh './gradlew codenarcMain'
                    sh './gradlew codenarcTest'
                    sh './gradlew codenarcIntegrationTest'
                }
            }
            post {
                always {
                    publishHTML([allowMissing: false,
                    alwaysLinkToLastBuild: false, 
                    keepAll: false, 
                    reportDir: 'build/reports/codenarc/', 
                    reportFiles: 'integrationTest.html, main.html, test.html', 
                    reportName: 'CodeNarc Reports', 
                    reportTitles: 'integrationTest, main, test'])
                }
            }
        }
    }
}
