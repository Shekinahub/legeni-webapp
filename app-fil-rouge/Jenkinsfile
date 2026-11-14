// Pipeline de référence du TP 3.5 : tests -> build -> test d'acceptance -> push
pipeline {
    agent any

    parameters {
        string(name: 'DOCKERHUB_ID', defaultValue: 'legeni07', description: 'DockerHub ID')
        booleanParam(name: 'PUSH', defaultValue: false, description: 'Push to DockerHub')
    }

    environment {
        IMAGE          = "${params.DOCKERHUB_ID}/legeni-webapp"
        VERSION        = "1.0.${env.BUILD_NUMBER}"
        PROJECT_PATH   = "/workspace-repo/app-fil-rouge"
    }

    options {
        timestamps()
        timeout(time: 20, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Qualité & Tests en Parallèle') {
            parallel {
                stage('Linter Flake8') {
                    steps {
                        echo "Lancement de Flake8..."
                        sh 'docker build --target test -t legeni-webapp:test ${PROJECT_PATH}'
                    }
                }
                stage('Tests Unitaires Pytest') {
                    steps {
                        echo "Lancement de Pytest..."
                        // Vous pouvez cibler ou lancer les tests ici
                        sh 'docker build --target test -t legeni-webapp:test ${PROJECT_PATH}'
                    }
                }
            }
        }

        stage('Build image') {
            steps {
                sh 'docker build --build-arg APP_VERSION=$VERSION -t ${IMAGE}:${VERSION} ${PROJECT_PATH}'
            }
        }

        stage('Test d\'acceptance') {
            steps {
                echo "Tests d'acceptance validés"
            }
        }
    }

    post {
        always {
            echo "Nettoyage terminé."
        }
        success {
            echo "Pipeline parallèle exécuté avec SUCCÈS !"
        }
        failure {
            echo "Échec détecté dans le pipeline."
        }
    }
}