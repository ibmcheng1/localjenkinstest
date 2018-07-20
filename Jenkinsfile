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
	                componentName: 'LocalJenkinsTest',
	                createComponent: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                    componentTemplate: 'HelmChartTemplate',
	               	    componentApplication: 'LocalJenkinsTest'
	            	],
	            	delivery: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                    pushVersion: '${BRANCH_NAME}.${BUILD_NUMBER}',
	                    //172.21.20.2
			    		//baseDir: 'workspace/LocalJenkinsTest_master-VKVNYMIITIN4CETXSG7S7NRTCOTWOWAUEQBM6UNPPTQHLS6TM7TA/chart/jenkinstest',
	                    
	                    // local linux
	                    baseDir: '/var/lib/jenkins/workspace/localjenkinstest_master-JYUH5UO2KHGJWNXDCV3GCFOBVM7BVUEQBWILN75V5WFRKN4M67FQ/chart/jenkinstest',
	                    
	                    fileIncludePatterns: '/**',
	                    fileExcludePatterns: '',
	                  
	                    pushDescription: 'Pushed from Jenkins',
	                    pushIncremental: false
	                ]			    
	            ]
             ])
	             
			     step([$class: 'UCDeployPublisher',
            		siteName: 'UCD-Server',
            		deploy: [
                		$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
                		deployApp: 'LocalJenkinsTest',
                		deployEnv: 'Dev',
                		deployProc: 'Deploy',
                		deployVersions: "LocalJenkinsTest:${BUILD_NUMBER}",
                		deployOnlyChanged: false
            		]
        		])
        		
        		step([$class: 'UCDeployPublisher',
            		siteName: 'UCD-Server',
            		deploy: [
                		$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
                		deployApp: 'LocalJenkinsTest',
                		deployEnv: 'Dev',
                		deployProc: 'Add Status to Version - SYSTEM_TEST_PASSED',
                		deployVersions: "LocalJenkinsTest:${BUILD_NUMBER}",
                		deployOnlyChanged: false
            		]
        		])

				
	}     	    

    }
	
