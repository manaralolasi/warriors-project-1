pipeline {

	agent any

	environment {
		AWS_ACCESS_KEY_ID     = credentials('Access_key_ID')
  		AWS_SECRET_ACCESS_KEY = credentials('Secret_access_key')
		AWS_S3_BUCKET = 'duihua'
		 ARTIFACT_NAME = "DuiHua-0.0.1-SNAPSHOT.war"
		AWS_EB_APP_NAME = 'duihua'
        AWS_EB_ENVIRONMENT_NAME = 'duihua-env1'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
	}

	stages {      
	stage('Build'){
            steps {
                sh "mvn clean"

                sh "mvn compile"
            }
        }

        
         stage('Test') {
            steps {
                
                sh "mvn test"

            }

            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml'

                }
            }
        }
     
        stage('Deploy') {
            steps {
                sh 'aws configure set region us-east-2	'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
        stage('Quality Scan'){
            steps {
                sh '''
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=DuiHua-project \
                    -Dsonar.host.url=http://ec2-13-58-24-113.us-east-2.compute.amazonaws.com:9000 \
                    -Dsonar.login=sqp_04f37387d2619adb4b4fc645fa588a2e662f212f
                '''
            }
        }
    }

}
