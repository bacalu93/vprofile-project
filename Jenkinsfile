pipeline {
    agent any
    tools {
        maven "MAVEN33"
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
        stage ("Preparation") {
            steps {
                script {
                    // Example: Replacing variables in settings.xml using a script
                    // This requires a template of settings.xml where variables are to be replaced
                    // Let's assume the template is named settings.xml.template
                    sh """
                    envsubst < settings.xml.template > settings.xml
                    """
                }
            }
        }
        stage ("Build") {
            steps {
                sh "mvn -s settings.xml -U clean install -DskipTests"
            }
        }
    }
}
