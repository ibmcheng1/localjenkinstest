def gitCommit
def volumes = [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock') ]
volumes += secretVolume(secretName: 'jenkins-docker-sec', mountPath: '/docker_reg_sec')
podTemplate(label: 'icp-liberty-build-2', slaveConnectTimeout: 600,
    containers: [
        containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:3.10-1'),
        containerTemplate(name: 'maven', image: 'maven:3.5.3-jdk-8', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'docker', image: 'docker:17.12', ttyEnabled: true, command: 'cat'),
    ],
    volumes: volumes
)
{
    node {
        //def gitCommit
        stage ('Extract') {
          checkout scm
          gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          echo "checked out git commit ${gitCommit}"
        }
 
        stage ('Push to UCD...') {		
            def imageTag = null
            imageTag = gitCommit
            sh """
            #!/bin/bash
	    pwd
	    ls -l
        echo "imageTag: ${imageTag}"
        echo "BUILD_NUMBER: ${BUILD_NUMBER}"
	    echo "WORKSPACE: ${WORKSPACE}"
        """
				
	    }     	    

    }
}	
