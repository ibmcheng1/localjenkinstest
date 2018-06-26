def volumes = [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock') ]
volumes += secretVolume(secretName: 'microclimate-registry-secret', mountPath: '/jenkins_docker_sec')
podTemplate(label: 'icp-liberty-build',
            nodeSelector: 'beta.kubernetes.io/arch=amd64',
    volumes: volumes
)
{
    node ('icp-liberty-build') {
        def gitCommit
        stage ('Extract') {
          checkout scm
          gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          echo "checked out git commit ${gitCommit}"
        }
 
	            stage ('Get Environment Information') {
            def imageTag = null
            imageTag = gitCommit
            sh """
            #!/bin/bash
            echo "imageTag: ${imageTag}"
            echo "checking file path"
            echo 
            pwd
            ls -l
            echo "finished"
            """
        }
        
        stage ('Push to UCD...') {
            def imageTag = null
            imageTag = gitCommit
            sh """
            #!/bin/bash
            echo "imageTag: ${imageTag}"
            echo "BUILD_NUMBER: ${BUILD_NUMBER}"
            """

	    step([$class: 'UCDeployPublisher',
	            siteName: 'UCD-Server',
	            component: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	                componentName: 'JenkinsTest',
	                createComponent: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                    componentTemplate: 'HelmChartTemplate',
	                	componentApplication: 'JenkinsTestApp'
	                ],
	                delivery: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                    pushVersion: '${BUILD_NUMBER}',
	                    baseDir: '.',
	                    fileIncludePatterns: 'workspace/*/**',
	                    fileExcludePatterns: '',
	                   
	                    pushDescription: 'Pushed from Jenkins',
	                    pushIncremental: false
	                ]
	            ]
        	])
		
	}     
        
    
 
	    
    }
}
