node {
    def dockerExe = 'C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    def imageName = 'riset321/simple-app'

    try {
        stage('Checkout Application') {
            echo 'Mengambil source code dari GitHub...'

            dir('app') {
                git branch: 'main',
                    url: 'https://github.com/arilix/simple-app.git'
            }
        }

        stage('Check Docker') {
            echo 'Memeriksa Docker CLI dan Docker Engine...'

            bat """
                "${dockerExe}" --version
                "${dockerExe}" info
            """
        }

        stage('Build Docker Image') {
            echo 'Membangun Docker image...'

            dir('app') {
                bat """
                    "${dockerExe}" build ^
                    -t ${imageName}:latest ^
                    -t ${imageName}:${env.BUILD_NUMBER} ^
                    .
                """
            }
        }

        stage('Login to Docker Hub') {
            echo 'Login menggunakan credential Jenkins...'

            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )
            ]) {
                powershell """
                    \$ErrorActionPreference = 'Stop'

                    \$env:DOCKERHUB_PASSWORD |
                        & '${dockerExe}' login `
                            --username \$env:DOCKERHUB_USERNAME `
                            --password-stdin

                    if (\$LASTEXITCODE -ne 0) {
                        exit \$LASTEXITCODE
                    }
                """
            }
        }

        stage('Push to Docker Hub') {
            echo 'Mengunggah Docker image ke Docker Hub...'

            bat """
                "${dockerExe}" push ${imageName}:latest
                if errorlevel 1 exit /b 1

                "${dockerExe}" push ${imageName}:${env.BUILD_NUMBER}
                if errorlevel 1 exit /b 1
            """
        }

        stage('Verify Image') {
            echo 'Memeriksa image yang tersedia secara lokal...'

            bat """
                "${dockerExe}" images ${imageName}
            """
        }

        currentBuild.result = 'SUCCESS'
        echo 'CI/CD PIPELINE BERHASIL!'

    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        echo "Pipeline gagal: ${e.getMessage()}"
        throw e
    }
}
