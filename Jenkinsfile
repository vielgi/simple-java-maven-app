pipeline {
    agent any
    environment {
        DOCKERFILE = "Dockerfile.build"
    }

    stages {
        stage("Build") {
            environment {
                MVN_COMMAND = "mvn clean package"
                TEST_REPORTS = "target/surefire-reports/*.xml"
            }

            parallel {

                /*stage('openjdk') {
                    agent {
                        docker { image 'openjdk' }
                    }
                    steps {
                        sh "${MVN_COMMAND} -P jdk13"
                    }
                }*/
                stage('custom') {
                    gitC()
                }
                stage("openjdk-8.0.232") {
                    agent {
                        dockerfile {
                            filename DOCKERFILE
                            additionalBuildArgs "--build-arg JAVA_VERSION=8.0.232-open -t maven:8.0.232-open"
                        }
                    }
                    steps {
                        sh "${MVN_COMMAND} -P jdk8"
                    }
                    post {
                        always {
                            junit TEST_REPORTS
                        }
                    }
                }

                stage("openjdk-11.0.5") {
                    agent {
                        dockerfile {
                            filename DOCKERFILE
                            additionalBuildArgs "--build-arg JAVA_VERSION=11.0.5-open -t maven:11.0.5-open"
                        }
                    }
                    steps {
                        sh "${MVN_COMMAND} -P jdk11"
                    }
                    post {
                        always {
                            junit TEST_REPORTS
                        }
                    }
                }
            }
        }
        stage('Confirm') {
            agent none
            steps {
                input(message: 'Confirm Installing', ok: "Yes, let's do it!")
            }
        }

        stage("Install") {
            agent {
                dockerfile {
                    filename DOCKERFILE
                    additionalBuildArgs "--build-arg JAVA_VERSION=11.0.5-open"
                    args '-v $HOME/.m2/repository:/home/jenkins/.m2/repository:rw,z'
                }
            }
            steps {
                sh "mvn -DskipTests install -P jdk11"
            }
        }
    }
}

def gitC() {
    println "${GIT_PREVIOUS_SUCCESSFUL_COMMIT}"
}
