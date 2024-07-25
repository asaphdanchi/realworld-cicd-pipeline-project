pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        NEXUS_REPO = 'maven-releases'
        NEXUS_CREDENTIAL_ID = 'nexus-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/your-repo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Tests') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Code Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                script {
                    def mavenPom = readMavenPom file: 'pom.xml'
                    def version = mavenPom.getVersion()
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: '3.90.191.241:8081',
                        groupId: mavenPom.getGroupId(),
                        version: "${version}",
                        repository: "${env.NEXUS_REPO}",
                        credentialsId: "${env.NEXUS_CREDENTIAL_ID}",
                        artifacts: [
                            [artifactId: mavenPom.getArtifactId(),
                             classifier: '',
                             file: "${WORKSPACE}/target/${mavenPom.getArtifactId()}-${version}.war",
                             type: 'war']
                        ]
                    )
                }
            }
        }
    }
}
