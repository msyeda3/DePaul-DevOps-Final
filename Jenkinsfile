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
                        withCredentials([string(credentialsId: 'nvd-api-key', variable: 'NVD_API_KEY')]) {
                            bat 'mvn org.owasp:dependency-check-maven:check -DnvdApiKey=%NVD_API_KEY% -DfailBuildOnCVSS=9 -DautoUpdate=false -DdependencyCheck.skip=true'
                        }
                    }
                }
                stage('Maven Dependency Audit') {
                    steps {
                        bat 'mvn versions:display-dependency-updates'
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed - checking results.'
        }
    }
}
