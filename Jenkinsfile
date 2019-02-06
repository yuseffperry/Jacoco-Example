pipeline {
    agent any
    environment {
	def mvnHome = tool name: 'maven', type: 'maven'
	def sonarqubeScannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }


    stages {
        stage('Build') {
            steps {
		    echo 'Building...'
		    sh '${mvnHome}/bin/mvn clean install -DskipTests'
            }
        }
        stage('Test') {
            steps {
		    echo 'Testing...'
		    sh '${mvnHome}/bin/mvn clean jacoco:prepare-agent install jacoco:report'
            }
        }
        /*stage('SonarQube Analysis') {
            steps {
		    echo 'SonarQube...'
		    withSonarQubeEnv('SonarQube') {
		    sh '${sonarqubeScannerHome}/bin/sonar-scanner'
	            }
            }
        }*/
        stage('Upload Snapshot to Nexus') {
            steps {
            script {
	       // withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jacocoexample-nexus-upload', usernameVariable: 'NEXUS_CREDENTIALS_USR', passwordVariable: 'NEXUS_CREDENTIALS_PSW']]) {
		    echo 'Nexus Snapshot...'
    
		    //sh '${mvnHome}/bin/mvn release:clean release:prepare release:perform -DreleaseVersion=1.0.0 -DdevelopmentVersion=1.0.1'

            sh '${mvnHome}/bin/mvn release:update-versions'
            sh '${mvnHome}/bin/mvn release:clean'
            sh '${mvnHome}/bin/mvn release:prepare'

            //Deploys Snapshot to http://localhost:8081/repository/maven-snapshots/
            sh '${mvnHome}/bin/mvn clean deploy'
                   // }
                }
            }
        }   
        /*stage('Upload Release to Nexus') {
            steps {
            input 'Upload Release?'

            echo 'Nexus Release...'

            sh "git push origin ${pom.artifactId}-${version}"
 		    //sh '${mvnHome}/bin/mvn release:clean'
		    //sh '${mvnHome}/bin/mvn release:prepare'
		    //sh '${mvnHome}/bin/mvn release:perform'
            }
        }*/
    }
}
