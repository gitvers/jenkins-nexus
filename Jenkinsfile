pipeline {
    agent any
    tools {
        maven "maven-3.8"
    }
   // environment {
     //   NEXUS_VERSION = "nexus3"
      //  NEXUS_PROTOCOL = "http"
       // NEXUS_URL = "3.142.141.22:8081"
       // NEXUS_REPOSITORY = "java-app"
       // NEXUS_CREDENTIAL_ID = "NEXUS_CRED"
    // }
     
     //   stage("Clone code from GitHub") {
       //   steps {
         //       script {
           //         git branch: 'main', credentialsId: 'githubwithpassword', url: 'https://github.com/devopshint/jenkins-nexus';
             //   }
           // }
       // }
    stages{
        stage("Maven Build") {
            steps {
                
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/gitvers/jenkins-nexus.git']]])
                    sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: '3.142.141.22:8081',
                            groupId: 'pom.com.mycompany.app',
                            version: 'pom.1.0-SNAPSHOT',
                            repository: 'maven-central-repo',
                            credentialsId: 'NEXUS_CRED',
                            artifacts: [
                                [artifactId: 'pom.my-app',
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}
