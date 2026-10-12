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
                echo 'Mengambil source code dari GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Memulai proses build di Windows...'
                bat 'echo "Build di Windows"'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Membangun Docker image...'

                bat '''
                    @echo off

                    "%DOCKER_EXE%" build -t "%IMAGE_NAME%:%BUILD_NUMBER%" .

                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Login dan mengunggah Docker image ke Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    powershell '''
                        $ErrorActionPreference = "Stop"

                        $docker = $env:DOCKER_EXE
                        $username = $env:DOCKER_USER
                        $password = $env:DOCKER_PASS
                        $image = $env:IMAGE_NAME
                        $tag = $env:BUILD_NUMBER

                        Write-Host "Login ke Docker Hub..."

                        $password | & $docker login `
                            --username $username `
                            --password-stdin

                        if ($LASTEXITCODE -ne 0) {
                            throw "Login Docker Hub gagal."
                        }

                        Write-Host "Login berhasil."
                        Write-Host "Push image dengan tag build..."

                        & $docker push "${image}:${tag}"

                        if ($LASTEXITCODE -ne 0) {
                            throw "Push image dengan tag build gagal."
                        }

                        Write-Host "Memberikan tag latest..."

                        & $docker tag "${image}:${tag}" "${image}:latest"

                        if ($LASTEXITCODE -ne 0) {
                            throw "Pemberian tag latest gagal."
                        }

                        Write-Host "Push image dengan tag latest..."

                        & $docker push "${image}:latest"

                        if ($LASTEXITCODE -ne 0) {
                            throw "Push image latest gagal."
                        }

                        Write-Host "Semua image berhasil diunggah."
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline berhasil!'
            echo 'Docker image telah diunggah ke Docker Hub.'
            echo 'Repository: https://hub.docker.com/r/riset321/simple-app'
        }

        failure {
            echo 'Pipeline gagal. Periksa Console Output untuk detail error.'
        }

        always {
            echo 'Pipeline selesai.'

            bat '''
                @echo off
                "%DOCKER_EXE%" logout
            '''
        }
    }
}
