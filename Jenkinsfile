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
        stage('QA') {
            steps {
                withGradle {
                    sh './gradlew codenarcMain'
                    sh './gradlew codenarcTest'
                    sh './gradlew codenarcIntegrationTest'
                    sh './gradlew check'
                }
                withSonarQubeEnv(credentialsId: 'local') {
                    withGradle {
                        sh './gradlew sonarqube'
                    }
                }
            }
            post {
                always {
                    recordIssues(
                        tools: [
                            pmdParser(pattern: 'build/reports/pmd/*.xml'),
                            checkStyle(pattern: 'build/reports/checkstyle/*.xml'),
                            codeNarc(pattern: 'build/reports/codenarc/*.xml')
                        ]
                    )

                }
            }
        }
    }
}
