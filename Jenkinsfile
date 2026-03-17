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
                sh 'mvn clean compile'
                echo 'Build Succeeded'
            }
        }
    }
}
