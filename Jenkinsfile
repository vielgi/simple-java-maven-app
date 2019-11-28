
pipeline {
    agent {
        label "docker"
    }
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

        stage("Install") {
            agent {
                dockerfile {
                    filename DOCKERFILE
                    additionalBuildArgs "--build-arg JAVA_VERSION=8.0.232-open"
                    args '-v $HOME/.m2/repository:/home/jenkins/.m2/repository:rw,z'
                }
            }
            steps {
                sh "mvn -DskipTests install -P jdk8"
            }
        }
    }
}