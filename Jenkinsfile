def gitCommit
def volumes = [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock') ]
volumes += secretVolume(secretName: 'jenkins-docker-sec', mountPath: '/docker_reg_sec')
podTemplate(label: 'icp-liberty-build-2', slaveConnectTimeout: 600,
    containers: [
        containerTemplate(name: 'jnlp', image: 'mycluster.icp:8500/default/jenkins/jnlp-slave:3.10-1'),
        containerTemplate(name: 'maven', image: 'mycluster.icp:8500/default/maven:3.5.4-jdk-8', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'docker', image: 'mycluster.icp:8500/default/docker:17.12', ttyEnabled: true, command: 'cat'),
    ],
    volumes: volumes
)
{
    node ('icp-liberty-build-2') {
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
