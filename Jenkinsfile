pipeline {
    agent any

    environment {

        AWS_ACCESS_KEY_ID     = credentials('aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')

        AWS_S3_BUCKET = "artifact-bucket-repo"
        ARTIFACT_NAME = "hello-world.war"
        AWS_EB_APP_NAME = "jave-webapp"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Javewebapp-env"
        SONAR_IP = "54.226.50.200"
        SONAR_TOKEN = "sqp_3d88d230105f36e9c2bdd5ca74dc393f77a6f770"

    }

    stages {
        stage('Validate') {
            steps {

                sh "mvn validate"

                sh "mvn clean"

            }
        }

         stage('Build') {
            steps {

                sh "mvn compile"

            }
        }

        stage('Test') {
            steps {

                sh "mvn test"

            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        
        stage('Quality Scan'){
            steps {
                sh '''
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=abdullah_almutaywia \
                    -Dsonar.host.url=http://$SONAR_IP \
                    -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }

        stage('Package') {
            steps {

                sh "mvn package"

            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false


                }
            }
        }

        stage('Publish artefacts to S3 Bucket') {
            steps {

                sh "aws configure set region us-east-1"

                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"

            }
        }

        stage('Deploy') {
            steps {

                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'

                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'


            }
        }

    }
} 
