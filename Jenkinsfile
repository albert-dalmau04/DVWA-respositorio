pipeline {
    agent any

    environment {
        // Nombre del servidor SonarQube configurado en Jenkins
        SONARQUBE_SERVER = 'sonarqube'
        // Agregar sonar-scanner al PATH
        PATH = "/opt/sonar-scanner/bin:${env.PATH}"
        SONAR_HOST_URL = 'http://10.30.212.9:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clonar el código fuente desde el repositorio
                checkout scm
            }
        }

        stage('ZAP Scan') {
            steps {
                script {
                    // Iniciar ZAP en un contenedor
                    def zapContainer = docker.run("zaproxy/zap-stable", "-u zap -p 8080:8080")
                    
                    // Ejecutar escaneo de ZAP (ajusta la URL a tu aplicación)
                    sh "curl 'http://10.30.212.9:8080/JSON/ascan/action/scan/?url=https://github.com/albert-dalmau04/DVWA-respositorio'"

                    // Esperar a que termine el escaneo
                    sleep(time: 60, unit: 'SECONDS')

                    // Detener el contenedor
                    sh "docker stop ${zapContainer.id}"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Configurar el entorno de SonarQube
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    // Ejecutar el análisis con SonarScanner
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=pipeline_sonarqube \
                        -Dsonar.sources=vulnerabilities \
                        -Dsonar.php.version=8.0
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Esperar el resultado del Quality Gate
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
