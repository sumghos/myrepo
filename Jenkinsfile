    pipeline {
		agent {
		label 'master'
		}
		tool {
		// This should be same as you have install in jenkins
		maven 'maven'

		}
		environment {
		// this can be nexus3 or nexus2
	     NEXUS_VERSION = "nexus3"
	     // This can be http or https
	     NEXUS_PROTOCOL = "http"
	     //where nexus3 is running
	     NEXUS_URL = "localhost:8081"
	     //Repo where we upload the artifact
	     NEXUS_REPOSITORY = "gamat-kurt"
	     //Jenkins credential to authenticate to nexus
	     NEXUS_CREDENTIAL = "nexus_credential"
	    }

		stages {
		    stage('Checkout') {
		        steps {
		        script {
		        // Let's clone the code
		       	checkout scm
		       	}
		        }
		    }
			stage('Build') {
		        steps {
		          script {
		          //If you are windows then you should use "bat" step
		          //since
					bat (/ ${M2_HOME}\bin\mvn install/)
		        }
		        }
			}
			stage("publish to nexus"){
			steps{
			script{
			//read POM.XML file using 'readMavenPom' step
			pom = readMavenPom file : "POM.xml"
			//find build artifact under target folder
			fileByGlob = findFile(glob :"target/*.${pom.packaging}")
			// print some information from the artifact found
			echo "${fileByGlob[0].name} ${fileByGlob[0].path} ${fileByGlob[0].directory} ${fileByGlob[0].length} ${fileByGlob[0].LastModified} "
			//extract the path from the file found
			artifactPath = fileByGlob[0].path;
			//Assign to a boolean response verifying if the artifact name exists
			artifactExists = fileExists artifactPath;
			if(artifactExists){
			echo "**** File : ${artifactPath}  Group : ${pom.groupID} Packaging : ${pom.packaging} Version : ${pom.version} ****"
			nexusArtifactUploader(
			nexusVersion : NEXUS_VERSION ;
			protocol : NEXUS_PROTOCOL;
			nexusUrl : NEXUS_URL;
			groupID : pom.groupID ;
			version : $ { BUILD_NUMBER } ;
			repository : NEXUS_REPOSITORY;
			credential : NEXUS_CREDENTIAL;
			artifact[
			//artifact generated such as jar , ear , war
			[
			artifactId : pom.artifact.Id,classifier : '',file: "pom.xml",type: "pom"
			]
			]
		    ) ;
		    }
			else {
			echo "**** File : ${artifactPath}, not found ****};
		   }
           }

		}

	}
}
}