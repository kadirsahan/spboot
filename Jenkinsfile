// install  nexus-artifact-uploader and pipeline-utility-steps....

pipeline {
    agent any

    tools {
            maven "3.6.0" // You need to add a maven with name "3.6.0" in the Global Tools Configuration page
        }
    stages {

        stage("clone code") {
                    steps {
                        script {
                            // Let's clone the source
                            git branch: 'main', url: 'https://github.com/kadirsahan/spboot.git'
                        }
                    }
         }

        stage("Build") {
            steps {
                sh "mvn -version"
                sh "mvn clean package -DskipTests -Dimage=nbkhello:${BUILD_NUMBER}"
            }
        }
        stage('Push Image To Nexus') {
            environment {
                DCKRHUB_ID = credentials("dockerhub_user_id")

                DCKRHUB_PWD = credentials("dockerhub_user_pwd")
            }

            steps {

                    sh 'docker login -u $DCKRHUB_ID -p $DCKRHUB_PWD'
                    sh 'docker tag nbkhello:${BUILD_NUMBER} kadirsahan/nbkhello:${BUILD_NUMBER}'
                    sh 'docker push kadirsahan/nbkhello:${BUILD_NUMBER}'

            }
        }

        stage('Deploy To Docker Swarm') {

            steps {
                    sh 'docker context ls'
                    sh 'docker context use nbkswarm'
                    sh 'docker context ls'
                    sh 'docker node ls'
                    sh 'sed -i "s/BUILD_NUMBER/${BUILD_NUMBER}/g" docker-compose.yaml'
                    sh 'docker-compose up -d'

            }
        }





    }

}
