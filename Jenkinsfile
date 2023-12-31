pipeline {
    agent any

    environment {
        function_name = 'jenkins'
    }

    stages {

        // CI Start
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }


        stage("SonarQube analysis") {
            agent any

            when {
                anyOf {
                    branch 'feature/*'
                    branch 'main'
                }
            }
            steps {
                 withSonarQubeEnv('sonar') {
        script {
            def scannerHome = tool 'sonar-scanner'
            withEnv(["PATH+SONAR=$scannerHome/bin"]) {
                sh 'mvn sonar:sonar -Dsonar.organization=Sarvesh545'
'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    try {
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    }
                    catch (Exception ex) {

                    }
                }
            }
        }

        stage('Push') {
            steps {
                echo 'Push'

                sh "aws s3 cp target/sample-1.0.3.jar s3://jav12"
            }
        }

        // Ci Ended

        // CD Started

        stage('Deployments') {
            parallel {

                stage('Deploy to Dev') {
                    steps {
                        echo 'Build'

                        sh "aws lambda update-function-code --function-name $function_name --region us-east-2 --s3-bucket jav12 --s3-key sample-1.0.3.jar"
                    }
                }

                stage('Deploy to test ') {
                    when {
                        branch 'main'
                    }
                    steps {
                        echo 'Build'

                        // sh "aws lambda update-function-code --function-name $function_name --region us-east-2 --s3-bucket jav12 --s3-key sample-1.0.3.jar"
                    }
                }
            }
        }


        

        // CD Ended
    }
}
