pipeline {
    //Picks any available agent. Change 'any' to node's label if present.
    agent any
    environment {
    //Uses local Jenkins maven located here: http://localhost:8080/configureTools/. Tool name must match name present here exactly.
	def mvnHome = tool name: 'maven', type: 'maven'
    //Uses local Jenkins sonar located here: http://localhost:8080/configureTools/. Tool name must match name present here exactly.
	def sonarqubeScannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }


    stages {
        /*stage('Build') {
            steps {
		    echo 'Building...'
            //Deletes the target directory and installs a new target directory to the local repo with includes the classes, test-classes, jar etc. -DskipTests, skips tests. To do jacoco tests along with install, remove -DskipTests and delete the test stage.
		    sh '${mvnHome}/bin/mvn clean install -DskipTests'
            }
        }
        stage('Test') {
            steps {
		    echo 'Testing...'
            //Uses jacoco-maven-plugin. Adds to target directory created during '${mvnHome}/bin/mvn clean install -DskipTests' to make test report. Additions include site, surefire-reports, and jacoco.exec.
		    sh '${mvnHome}/bin/mvn jacoco:prepare-agent install jacoco:report'
            }
        }
        stage('SonarQube Analysis') {
            steps {
		    echo 'SonarQube...'
            //Located at http://localhost:8080/configure, name must match exactly.
		    withSonarQubeEnv('SonarQube') {
            //Uses sonar-scanner.properties file. Uploads project analysis to SonarQube: http://localhost:9000/.
		    sh '${sonarqubeScannerHome}/bin/sonar-scanner'
	            }
            }
        }*/
        stage('Upload Snapshot to Nexus') {
            steps {
            script {
            //Not needed now, meant for OpenShift.
	        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jacocoexample-nexus-upload', usernameVariable: 'NEXUS_CREDENTIALS_USR', passwordVariable: 'NEXUS_CREDENTIALS_PSW']]) {
		    echo 'Nexus Snapshot...'
            //Updates versions, could be useful if we go this route: https://stackoverflow.com/questions/53098369/shell-command-to-update-pom-file-from-a-variable/53099850#53099850
            //sh '${mvnHome}/bin/mvn release:update-versions'

            def pom = readMavenPom file: 'pom.xml'

            //Deploys Snapshot to http://localhost:8081/repository/maven-snapshots/
            sh '${mvnHome}/bin/mvn deploy'

            //Change minor version (Reference: https://stackoverflow.com/questions/51126046/jenkins-can-parsedversion-property-be-used-in-pipeline-script)
            sh "mvn build-helper:parse-version versions:set -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.nextMinorVersion}.${env.BUILD_NUMBER}-SNAPSHOT versions:commit"

            sh "ls -la"
            sh "pwd"

            //Commit version changes to GitHub using git commit command. "Checkout to specific local branch" in the "Additional Behaviors" section to set the "Branch name" to master as well.
            //sh "git commit -am 'SNAPSHOT Update from Jenkins'" 
                    }
                }
            }
        }   
        stage('Upload Release to Nexus') {
            steps {
            script{
            input 'Upload Release to Nexus?'

            echo 'Nexus Release...'

           /*
            * Clean any locally modified files and ensure we are actually on origin/master
            * as a failed release could leave the local workspace ahead of origin/master
            */
            def pom = readMavenPom file: 'pom.xml'
            def version = pom.version.replace("-SNAPSHOT", ".${currentBuild.number}")

            sh "git clean -f && git reset --hard origin/master"

            //Deletes pom.xml.releaseBackup and release.properties files.
            sh '${mvnHome}/bin/mvn release:clean'

           /*
            * Prepare for a release in SCM. Checks to see if local and remote files are in sync as well.
		    * They must be in sync or this command will not work (i.e. 'https://github.com': Everything up-to-date)
            */

            /*
            sh """mvn build-helper:parse-version versions:set \
                -DreleaseVersion=${version} \
                -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.nextMinorVersion}.${currentBuild.number}-SNAPSHOT \
                versions:commit \
                -DpushChanges=false \
                -DlocalCheckout=true \
                -DpreparationGoals=initialize \
                -Darguments="-DskipTests" \
                release:prepare
            """
            */
           /*
            * Grabs release from SCM (i.e. https://github.com/.../.../releases) made from '${mvnHome}/bin/mvn release:prepare',
		    * and uploads it to nexus-releases: http://localhost:8081/repository/maven-releases/ if it is not already present.
            * If file with the same version is present, then it will not work.
            */
            sh '${mvnHome}/bin/mvn initialize release:prepare release:perform'

            //Push release
            //sh "git push origin ${pom.artifactId}-${version}"

            //Hardcoded for versioning for testing purposes.
		    //sh '${mvnHome}/bin/mvn release:clean release:prepare release:perform -DreleaseVersion=1.0.0 -DdevelopmentVersion=1.0.1'

            //Prepare for a release in SCM. Checks to see if local and remote files are in sync as well. They must be in sync or this command will not work (i.e. 'https://github.com': Everything up-to-date)
		    //sh '${mvnHome}/bin/mvn release:prepare -DdryRun=true'
            //Grabs release from SCM (i.e. https://github.com/.../.../releases) made from '${mvnHome}/bin/mvn release:prepare', and uploads it to nexus-releases: http://localhost:8081/repository/maven-releases/ if it is not already present. If file with the same version is present, then it will not work.
		    //sh '${mvnHome}/bin/mvn release:perform'
                }
            }
        }
    }
}
