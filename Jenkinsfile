pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    environment {
        SNAP_REPO = "vprofile-snapshot"
        NEXUS_USER = "admin"
        NEXUS_PASS = "19932"
	RELEASE_REPO = "vprofile-release"
	CENTRAL_REPO = "vpro-maven-central"
        NEXUSIP = "52.58.4.128"
	NEXUSPORT = "8081"
	NEXUS_GRP_REPO = "vpro-maven-group"
        NEXUS_CREDENTIAL_ID = "nexuslogin"
    } 


    stages {
        stage ("Build") {
            steps {
                sh "mvn -s settings.xml -U clean install -DskipTests"
            }
        }
    }
}
