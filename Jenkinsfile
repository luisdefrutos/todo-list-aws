pipeline {
    agent any

    environment {
        PYTHONPATH = "${env.WORKSPACE}"
        PATH = "/home/ubuntu/.local/bin:/usr/local/bin:${env.PATH}"
    }

    stages {
        stage('Preparar entorno') {
            steps {
                echo 'Instalando dependencias...'
                sh 'pip install --user boto3 pytest flake8 bandit moto'
            }
        }

        stage('Análisis de estilo (Flake8)') {
            steps {
                echo 'Ejecutando Flake8...'
                sh 'flake8 src test > flake8-report.txt || true'
            }
        }

        stage('Análisis de seguridad (Bandit)') {
            steps {
                echo 'Ejecutando Bandit...'
                sh 'bandit -r src -f html -o bandit-report.html || true'
            }
        }

        stage('Tests Unitarios') {
            steps {
                echo 'Ejecutando tests unitarios...'
                sh 'pytest test/unit/TestToDo.py --junitxml=unit-tests.xml || true'
            }
        }

        stage('Tests de Integración') {
            steps {
                echo 'Ejecutando tests de integración...'
                sh 'pytest test/integration/todoApiTest.py --junitxml=integration-tests.xml || true'
            }
        }
    }

    post {
        always {
            echo 'Archivando resultados...'
            archiveArtifacts artifacts: 'flake8-report.txt', allowEmptyArchive: true
            archiveArtifacts artifacts: 'bandit-report.html', allowEmptyArchive: true
            archiveArtifacts artifacts: 'unit-tests.xml', allowEmptyArchive: true
            archiveArtifacts artifacts: 'integration-tests.xml', allowEmptyArchive: true

            junit 'unit-tests.xml'
            junit 'integration-tests.xml'
        }

        success {
            echo 'Pipeline CI ejecutado correctamente.'
        }

        failure {
            echo 'Error en el pipeline CI.'
        }
    }
}
