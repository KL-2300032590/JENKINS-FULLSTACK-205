pipeline {
    agent any

    stages {

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('STUDENTAPI-REACT') {
                    sh '''
                    #!/bin/bash

                    # Manually add Node/npm to PATH
                    export PATH=$PATH:/opt/homebrew/bin

                    # Sanity check
                    echo "Using Node: $(which node)"
                    echo "Using npm: $(which npm)"

                    # Build steps
                    npm install || { echo "npm install failed"; exit 1; }
                    npm run build || { echo "npm run build failed"; exit 1; }
                    '''
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                TOMCAT_DIR="/Users/pardhasaradhireddy/apache-tomcat-10.1.43/webapps/reactstudentapi"
                
                # Stop Tomcat
                /Users/pardhasaradhireddy/apache-tomcat-10.1.43/bin/shutdown.sh || true
                
                # Remove old deployment
                rm -rf "$TOMCAT_DIR"
                
                # Create fresh directory
                mkdir -p "$TOMCAT_DIR"
                
                # Copy build output
                cp -R STUDENTAPI-REACT/dist/* "$TOMCAT_DIR/"
                
                # Start Tomcat
                /Users/pardhasaradhireddy/apache-tomcat-10.1.43/bin/startup.sh
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('STUDENTAPI-SPRINGBOOT') {
                    sh '''
                    #!/bin/bash

                    # Manually add Maven to PATH
                    export PATH=$PATH:/Users/pardhasaradhireddy/maven/bin

                    # Sanity check
                    echo "Using Maven: $(which mvn)"
                    mvn -v || { echo "Maven not found"; exit 1; }

                    # Build backend
                    mvn clean package || { echo "Maven build failed"; exit 1; }
                    '''
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                TOMCAT_WEBAPPS="/Users/pardhasaradhireddy/apache-tomcat-10.1.43/webapps"
                WAR_FILE="springbootstudentapi.war"

                # Stop Tomcat
                /Users/pardhasaradhireddy/apache-tomcat-10.1.43/bin/shutdown.sh || true

                # Remove old deployment
                rm -f "$TOMCAT_WEBAPPS/$WAR_FILE"
                rm -rf "$TOMCAT_WEBAPPS/springbootstudentapi"

                # Copy new WAR
                cp STUDENTAPI-SPRINGBOOT/target/*.war "$TOMCAT_WEBAPPS/"

                # Start Tomcat
                /Users/pardhasaradhireddy/apache-tomcat-10.1.43/bin/startup.sh
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}