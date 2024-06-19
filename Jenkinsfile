pipeline {
    agent none
    stages {
        stage('Integration UI Test') {
            parallel {
                stage('Deploy') {
                    agent any
                    steps {
                        // Run the Docker container for the PHP application
                        sh '''
                        docker run -d -p 80:80 --name my-apache-php-app -v /home/user/Desktop/LAB7B/jenkins-php-selenium-test/src:/var/www/html php:7.2-apache
                        sleep 1
                        echo 'Now...'
                        echo 'Visit http://localhost to see your PHP application in action.'
                        '''
                        input message: 'Finished using the website? (Click "Proceed" to continue)'
                        // Stop and remove the Docker container
                        sh '''
                        docker kill my-apache-php-app
                        docker rm my-apache-php-app
                        '''
                    }
                }
                stage('Headless Browser Test') {
                    agent {
                        docker {
                            image 'maven:3-alpine'
                            args '-v /root/.m2:/root/.m2'
                        }
                    }
                    steps {
                        sh 'mvn -B -DskipTests clean package'
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }
            }
        }
    }
}
