def workingDirectory = '.'
environment {
        HELM_DIR = '/home/jenkins/workspace'
}

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
          echo "workingDirectory: " + workingDirectory
          checkout scm
          gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          echo "checked out git commit ${gitCommit}"
        }
 
        stage ('Push to UCD...') {		
            def imageTag = null
            imageTag = gitCommit
            sh """
            #!/bin/bash
	    echo "workingDirectory 3: " + workingDirectory
	    echo "Current directoy: "
	    pwd
	    ls -l
            echo "imageTag: ${imageTag}"
            echo "BUILD_NUMBER: ${BUILD_NUMBER}"
	    echo "WORKSPACE: ${WORKSPACE}"
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
			    baseDir: 'workspace/JenkinsUCDtest_master-GSJH5RUKHTMOJOZ56VZPJHYWVWHRTNGSXAWNZC7U3VUJCVM4XMDQ/chart/jenkinstest',
	                    fileIncludePatterns: '/**',
	                    fileExcludePatterns: '',
	                  
	                    pushDescription: 'Pushed from Jenkins',
	                    pushIncremental: false
	                ]
			 
			deploy: [
	            	    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
	            	    deployApp: 'JenkinsTestApp',
	                    deployEnv: 'Dev',
	            	    deployProc: 'Deploy Application',
	            	    createProcess: [
	                	$class: 'com.urbancode.jenkins.plugins.ucdeploy.ProcessHelper$CreateProcessBlock',
	                        processComponent: 'Deploy'
	            	    ],
	           	    deployVersions: 'Jenkins:${BUILD_NUMBER}',
	                    deployOnlyChanged: false
        		]
			    
	            ]
             ])
		
	}     

	    

	    

    }
}
