pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/bin'
        SONAR_TOKEN = credentials('jenkins')
        DOCKER_HUB_USER = 'hamza2011'
        DOCKER_IMAGE = 'springboot_devops'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/hamza-181/devops.git', branch: 'main'
                echo '✅ Checkout completed successfully!'
            }
        }

        stage('Clean') {
            steps {
                sh 'mvn clean'
                echo '✅ Clean completed successfully!'
            }
        }

        stage('Validate') {
            steps {
                sh 'mvn validate'
                echo '✅ Validate completed successfully!'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
                echo '✅ Package completed successfully!'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    try {
                        withSonarQubeEnv('sonarqube') {
                            sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
                        }
                        echo '✅ SonarQube analysis completed successfully!'
                    } catch (err) {
                        echo "⚠️ SonarQube analysis failed: ${err}"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest ."
                    echo '✅ Docker image built successfully!'
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_HUB_PASS')]) {
                        sh "echo $DOCKER_HUB_PASS | docker login -u ${DOCKER_HUB_USER} --password-stdin"
                    }
                    sh "docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest"
                    echo '✅ Docker image pushed to DockerHub successfully!'
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
        always {
            script {
                sh 'docker logout || true'
            }
        }
    }
}
