pipeline {
    agent any
    parameters {
        string(name: 'PARAMETER_1', defaultValue: 'default', description: 'A sample string parameter')
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') { 
            steps {
                sh 'dotnet restore' 
                sh 'dotnet build --no-restore' 
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test --no-build --no-restore --collect "XPlat Code Coverage"'
            }
            post {
                always {
                    recordCoverage(tools: [[parser: 'COBERTURA', pattern: '**/*.xml']], sourceDirectories: [[path: 'SimpleWebApi.Test/TestResults']])
                }
            }
        }
        stage('Deliver') {
            steps {
                sh 'dotnet publish SimpleWebApi --no-restore -o published'
            }
            post {
                success {
                    archiveArtifacts 'published/*.*'
                }
            }
        }
        stage('Summary') {
            environment {
                SECRET_KEY = credentials('foo-creds')
            }
            steps {
                echo "PARAMETER_1 value: ${params.PARAMETER_1}"
                echo "${SECRET_KEY}"
            }
        }
    }
}
