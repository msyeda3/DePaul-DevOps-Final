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
                        bat 'mvn org.owasp:dependency-check-maven:check'
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
            echo 'Pipeline execution finished. Archiving available reports...'
            // This replaces the missing 'dependencyCheckPublisher'
            archiveArtifacts artifacts: 'target/dependency-check-report.xml', allowEmptyArchive: true
            
            // Optional: You can also archive your JaCoCo coverage reports here
            archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
        }
    }
} // Final closing brace for the whole pipeline
