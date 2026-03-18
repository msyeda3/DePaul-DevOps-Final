pipeline {
    agent any

    stages {
        stage('Stage A - Build') {
            steps {
                bat 'mvn clean compile'
            }
        }
        stage('Stage B - Test') {
            steps {
                bat 'mvn test'
            }
        }
        stage('Stage C - Parallel Scanning') {
            parallel {
                stage('OWASP Dependency Check') {
                    steps {
                        // Using skip=true for lab purposes to avoid NVD timeouts
                        bat 'mvn org.owasp:dependency-check-maven:check -DdependencyCheck.skip=true'
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
            echo 'Pipeline execution finished. Checking post-build steps...'
            dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
        }
    }
}
