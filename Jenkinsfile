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
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "52.58.4.128:8081"
        NEXUS_REPOSITORY = "vprofile-release"
	    NEXUS_GRP_REPO    = "vprofile-maven-group"
        NEXUS_CREDENTIAL_ID = "nexuslogin"
        // ARTVERSION = "${env.BUILD_ID}"
    } 


    stages {
        stage ("Build") {
            steps {
                sh "mvn -s setting.xml -DskipTests install"
            }
        }
    }
}
