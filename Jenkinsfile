pipeline {
    agent any

    environment {
        // Nombre del servidor SonarQube configurado en Jenkins
        SONARQUBE_SERVER = 'sonarqube'
        // Agregar sonar-scanner al PATH
        PATH = "/opt/sonar-scanner/bin:${env.PATH}"

        SONAR_HOST_URL = 'http://10.30.212.9:9000'
        SONAR_AUTH_TOKEN = credentials('squ_1df8e5876ba943101e1b4069e799f5e3e523473b') // ID de la credencial
    }

    stages {
        stage('Checkout') {
            steps {
                // Clonar el código fuente desde el repositorio
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // Configurar el entorno de SonarQube
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    // Ejecutar el análisis con SonarScanner
                    sh 
                        sonar-scanner \
                        -Dsonar.projectKey=pipeline_sonarqube \
                        -Dsonar.sources=vulnerabilities \
                        -Dsonar.php.version=8.0
                    
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
