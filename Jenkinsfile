pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/HaiderZafar20/maven-web-application.git'
            }
        }

        stage('Clean Workspace') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {

                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=maven-web-application \
                    -Dsonar.projectName=maven-web-application
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Package WAR') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh """
                echo "Workspace is: ${env.WORKSPACE}"
                ls -l ${env.WORKSPACE}/target/

                docker cp ${env.WORKSPACE}/target/maven-web-application.war tomcat:/usr/local/tomcat/webapps/maven-web-application.war
                """
            }
        }
        stage('Verify Deployment') {
            steps {
                echo 'Application deployed successfully!'
                echo 'Access application at:'
                echo 'http://35.159.69.22:8090/maven-web-application'
            }
        }
    }

    post {

        success {
            echo 'CI/CD Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline execution failed.'
        }

        /*
        always {
            cleanWs()
        }
        */
    }
}