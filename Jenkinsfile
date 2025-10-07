pipeline {
    agent any

    environment {
        // Tomcat container name and path inside container
        TOMCAT_CONTAINER = 'tomcat9'
        TOMCAT_WEBAPPS = '/usr/local/tomcat/webapps'
        // Application name (change to match your project)
        APP_NAME = 'myapp'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building WAR file...'
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat (Docker)') {
            steps {
                echo 'Deploying WAR file to Tomcat container...'
                sh '''
                    WAR_FILE=target/${APP_NAME}.war
                    if [ ! -f "$WAR_FILE" ]; then
                        echo "WAR file not found!"
                        exit 1
                    fi

                    echo "Copying WAR to Tomcat container..."
                    docker cp $WAR_FILE ${TOMCAT_CONTAINER}:${TOMCAT_WEBAPPS}/

                    echo "Restarting Tomcat container..."
                    docker restart ${TOMCAT_CONTAINER}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking Tomcat deployment...'
                sh '''
                    sleep 10
                    curl -I http://localhost:8085/${APP_NAME}/ || true
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. Check Jenkins logs.'
        }
    }
}
