pipeline {
    agent none
    stages {
        stage('Integration UI Test') {
            parallel {
                stage('Deploy') {
                    agent any
                    steps {
                        sh './jenkins/scripts/deploy.sh'
                        input message: 'Finished using the web site? (Click "Proceed" to continue)'
                        sh './jenkins/scripts/kill.sh'
                    }
                }
                stage('Headless Browser Test') {
                    agent { // Agent make it such that the 'sh' command in steps and post will be entered into maven container.
                        docker {
                            image 'arm64v8/maven'
                            args '-v /root/.m2:/root/.m2' // Mount to cache Maven dependencies to between builds to speed up the build process.
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
