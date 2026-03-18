pipeline {
    agent any

    stages {
        stage('Stage A - Build') {
            steps {
                bat 'mvn clean compile'
                echo 'Build Succeeded'
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
                        withCredentials([string(credentialsId: 'nvd-api-key', variable: 'NVD_API_KEY')]) {
                            // Using -DdependencyCheck.skip=true to bypass NVD download issues for the lab
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
            // This 'echo' is required so the block isn't empty (Fixes Build #13/#15)
            echo 'Pipeline execution finished. Checking post-build steps...'
        }
    }
}
