pipeline {
    agent any
    environment {
	def mvnHome = tool name: 'maven', type: 'maven'
	def sonarqubeScannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }


    stages {
        /*stage('Build') {
            steps {
		    echo 'Building...'
		    sh '${mvnHome}/bin/mvn install'
            }
        }*/
        stage('Test') {
            steps {
		    echo 'Testing...'
		    sh '${mvnHome}/bin/mvn clean jacoco:prepare-agent install jacoco:report'
		    /*junit allowEmptyResults: true, testResults: 'target/test-results/test/*.xml'
		    publishHTML([allowMissing: true,
		      alwaysLinkToLastBuild: true,
		      keepAll: false,
		      reportDir: 'target/reports/tests/test',
		      reportFiles: 'index.html'
		      reportName: 'Jacoco Exmaple Test Results',
		      reportTitles: ''
		   ])*/
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
        stage('Publish to Nexus') {
            steps {
		    echo 'Nexus...'
		    sh '${mvnHome}/bin/mvn clean deploy -Dmaven.test.skip=true'
		    sh '${mvnHome}/bin/mvn release:clean'
		    sh '${mvnHome}/bin/mvn release:prepare'
		    sh '${mvnHome}/bin/mvn release:perform'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
