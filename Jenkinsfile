pipeline {
    agent any
    tools {
        maven 'M3'
        jdk 'OpenJDK 11'
    }
    stages {
        stage('Stage A - Checkout') {
            steps {
                checkout scm
                echo 'Checkout Succeeded'
            }
        }
        stage('Stage B - Build') {
            steps {
                bat 'mvn clean compile'
                echo 'Build Succeeded'
            }
        }
        stage('Stage C - Parallel Scanning') {
            parallel {
                stage('OWASP Dependency Check') {
                    steps {
                        // This identifies vulnerable libraries
                        bat "mvn org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=9"
                    }
                }
                stage('Dependency Audit') {
                    steps {
                        // This checks for outdated versions
                        bat 'mvn versions:display-dependency-updates'
                    }
                }
            }
        }
    }
}
