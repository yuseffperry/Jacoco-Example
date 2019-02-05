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
	        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jacocoexample-nexus-upload', usernameVariable: 'NEXUS_CREDENTIALS_USR', passwordVariable: 'NEXUS_CREDENTIALS_PSW']]) {
		    echo 'Nexus Snapshot...'

            //Deploys Snapshot to http://localhost:8081/repository/maven-snapshots/
            sh '${mvnHome}/bin/mvn clean deploy -Dmaven.test.skip=true'
            //sh '${mvnHome}/bin/mvn release:clean'
		    //sh '${mvnHome}/bin/mvn release:prepare release:perform -DreleaseVersion=${releaseVersion} -DdevelopmentVersion=${developmentVersion}'

            //def pom = readMavenPom file: 'pom.xml'
            //def version = pom.version.replace("-SNAPSHOT", ".${currentBuild.number}")

           /*
            * Clean any locally modified files and ensure we are actually on origin/master
            * as a failed release could leave the local workspace ahead of origin/master
            */

            //sh "git clean -f && git reset --hard origin/master"

            //sh '${mvnHome}/bin/mvn release:prepare'

            /*sh """
                mvn \
                -DreleaseVersion=${version} \
                -DdevelopmentVersion=${pom.version} \
                -DpushChanges=false \
                -DlocalCheckout=true \
                -DpreparationGoals=initialize \
                -Darguments="-DskipTests" \
                release:prepare release:perform
            """*/
                    }
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
