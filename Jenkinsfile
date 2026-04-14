// ─────────────────────────────────────────────────────────────────────────
// Jenkinsfile — Worker Finder CI/CD Pipeline
// Run this on Jenkins (local laptop via ngrok OR cloud VM)
// ─────────────────────────────────────────────────────────────────────────

pipeline {
    agent any

    tools {
        maven 'Maven-3.9'   // Configure this name in Jenkins → Global Tools
        jdk   'JDK-17'      // Configure this name in Jenkins → Global Tools
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')   // Add in Jenkins Credentials
        IMAGE_NAME = "${DOCKERHUB_CREDENTIALS_USR}/worker-finder-api"
        IMAGE_TAG  = "build-${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '--- Checking out source code ---'
                checkout scm
            }
        }

        stage('Backend: Compile') {
            steps {
                echo '--- Compiling Spring Boot backend ---'
                dir('rest-api') {
                    sh 'mvn clean compile -B'
                }
            }
        }

        stage('Backend: Unit Tests') {
            steps {
                echo '--- Running JUnit tests ---'
                dir('rest-api') {
                    sh 'mvn test -B'
                }
            }
            post {
                always {
                    junit 'rest-api/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Frontend: Build') {
            steps {
                echo '--- Building React frontend ---'
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run build'
                }
            }
        }

        stage('Backend: Package JAR') {
            steps {
                echo '--- Packaging Spring Boot JAR ---'
                dir('rest-api') {
                    sh 'mvn package -DskipTests -B'
                }
            }
        }

        stage('Docker: Build Image') {
            when {
                branch 'main'
            }
            steps {
                echo '--- Building Docker image ---'
                dir('rest-api') {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                    sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Docker: Push to Hub') {
            when {
                branch 'main'
            }
            steps {
                echo '--- Pushing image to Docker Hub ---'
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push ''' + "${IMAGE_NAME}:${IMAGE_TAG}" + '''
                    docker push ''' + "${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy to Staging') {
            when {
                branch 'main'
            }
            steps {
                echo '--- Triggering deployment on Render ---'
                sh 'curl -X POST $RENDER_DEPLOY_HOOK'
                // Set RENDER_DEPLOY_HOOK as an env variable in Jenkins
            }
        }
    }

    post {
        success {
            echo "Pipeline PASSED — Build #${BUILD_NUMBER} succeeded"
        }
        failure {
            echo "Pipeline FAILED — Check logs above"
        }
    }
}
