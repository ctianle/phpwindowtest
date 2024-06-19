pipeline {
    agent none
    stages {
        stage('Integration UI Test') {
            parallel {
                stage('Deploy') {
                    agent any
                    steps {
                        sh '''
                        docker run -d -p 80:80 --name my-apache-php-app -v /home/user/Desktop/LAB7B/jenkins-php-selenium-test/src:/var/www/html php:7.2-apache
                        sleep 1
                        echo 'Now...'
                        echo 'Visit http://localhost to see your PHP application in action.'
                        '''
                        input message: 'Finished using the website? (Click "Proceed" to continue)'
                        sh '''
                        docker kill my-apache-php-app
                        docker rm my-apache-php-app
                        '''
                    }
                }
                stage('Headless Browser Test') {
                    agent any
                    steps {
                        sh '''
                        docker pull arm64v8/maven
                        docker run --rm -v /root/.m2:/root/.m2 -v $(pwd):/workspace -w /workspace arm64v8/maven mvn -B -DskipTests clean package
                        docker run --rm -v /root/.m2:/root/.m2 -v $(pwd):/workspace -w /workspace arm64v8/maven mvn test
                        '''
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
