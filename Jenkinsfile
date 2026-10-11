pipeline {
    agent any

    environment {
        DOCKER_EXE = 'C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE_NAME = 'riset321/simple-app'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'echo "Build di Windows"'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                    "%DOCKER_EXE%" build -t "%IMAGE_NAME%:%BUILD_NUMBER%" .
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                        @echo off

                        powershell.exe -NoProfile -NonInteractive -ExecutionPolicy Bypass -Command "$env:DOCKER_PASS | & $env:DOCKER_EXE login --username $env:DOCKER_USER --password-stdin; if ($LASTEXITCODE -ne 0) { exit $LASTEXITCODE }; exit 0"

                        if errorlevel 1 exit /b 1

                        "%DOCKER_EXE%" push "%IMAGE_NAME%:%BUILD_NUMBER%"
                        if errorlevel 1 exit /b 1

                        "%DOCKER_EXE%" tag "%IMAGE_NAME%:%BUILD_NUMBER%" "%IMAGE_NAME%:latest"
                        if errorlevel 1 exit /b 1

                        "%DOCKER_EXE%" push "%IMAGE_NAME%:latest"
                        if errorlevel 1 exit /b 1
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Selesai build'
            bat '''
                @echo off
                "%DOCKER_EXE%" logout
            '''
        }
    }
}
