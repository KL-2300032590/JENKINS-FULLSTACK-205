pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:/Users/pardhasaradhireddy/maven/bin:${env.PATH}"
        TOMCAT_HOME = "/Users/pardhasaradhireddy/apache-tomcat-10.1.43"
    }

    stages {

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('STUDENTAPI-REACT') {
                    sh '''
                    npm install
                    npm run build
                    '''
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                FRONTEND_PATH="$TOMCAT_HOME/webapps/reactstudentapi"

                # Remove old frontend
                rm -rf "$FRONTEND_PATH"
                mkdir -p "$FRONTEND_PATH"

                # Detect build output folder
                if [ -d "STUDENTAPI-REACT/build" ]; then
                    cp -R STUDENTAPI-REACT/build/* "$FRONTEND_PATH/"
                elif [ -d "STUDENTAPI-REACT/dist" ]; then
                    cp -R STUDENTAPI-REACT/dist/* "$FRONTEND_PATH/"
                else
                    echo "No frontend build output found!"
                    exit 1
                fi
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('STUDENTAPI-SPRINGBOOT') {
                    sh '''
                    mvn clean package
                    '''
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                WEBAPPS_PATH="$TOMCAT_HOME/webapps"

                rm -f "$WEBAPPS_PATH/springbootstudentapi.war"
                rm -rf "$WEBAPPS_PATH/springbootstudentapi"

                cp STUDENTAPI-SPRINGBOOT/target/*.war "$WEBAPPS_PATH/"
                '''
            }
        }

        // ===== RESTART TOMCAT =====
        stage('Restart Tomcat') {
            steps {
                sh '''
                $TOMCAT_HOME/bin/shutdown.sh || true
                sleep 3
                $TOMCAT_HOME/bin/startup.sh
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
