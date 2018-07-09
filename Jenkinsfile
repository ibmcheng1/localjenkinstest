    node {
        def gitCommit
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
	    step([$class: 'UCDeployPublisher',
	            siteName: 'UCD-Server',
	            component: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	                componentName: 'JenkinsTest',
	                createComponent: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                    componentTemplate: 'HelmChartTemplate',
	               	    componentApplication: 'LocalJenkinsTest'
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
	            ]
             ])
	
	     step([$class: 'UCDeployPublisher',
	            siteName: 'UCD-Server',
	            component: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	                componentName: 'JenkinsTest',
			deploy: [
	            	    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
	            	    deployApp: 'LocalJenkinsTest',
	                    deployEnv: 'Dev',
	            	    deployProc: 'Deploy Application',
	            	    createProcess: [
	                	$class: 'com.urbancode.jenkins.plugins.ucdeploy.ProcessHelper$CreateProcessBlock',
	                        processComponent: 'Deploy'
	            	    ],
	           	    deployVersions: '${BUILD_NUMBER}',
	                    deployOnlyChanged: false
        		]
			    
	            ]
             ])	
				
	}     	    

    }
	
