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
	    echo "---------------------------"
	    WORKING_DIR=${PWD}
	    OFFSET_DIR=chart/jenkinstest

	    echo WORKING_DIR = ${WORKING_DIR}
	    echo OFFSET_DIR = ${OFFSET_DIR}

	    cd ./${OFFSET_DIR}
	    echo "CURRENT Directoy: ${PWD}"	    
	    	IMAGE_TAG=${imageTag}
		TARGET_FILE=values.yaml
		TEMPLATE=values.template
		OUTPUT_FILE=values.yaml.out
		BUILD_PROPERTIES_FILE=build.properties
		TARGET_BUILD_PROPERTIES_FILE=${WORKING_DIR}/${OFFSET_DIR}/${BUILD_PROPERTIES_FILE}

		echo IMAGE_TAG = ${IMAGE_TAG}
		echo TARGET_FILE = ${TARGET_FILE}
		echo TARGET_BUILD_PROPERTIES_FILE = ${TARGET_BUILD_PROPERTIES_FILE}

		echo "update TAG before: "
		cat ${TARGET_FILE} | grep tag:

		TAG_OLD_String="@@@TAG@@@"
		TAG_NEW_String=${IMAGE_TAG}
		sed -e "s|${TAG_OLD_String}|${TAG_NEW_String}|g" ${TARGET_FILE} > ${TARGET_FILE}.temp 
		cp ${TARGET_FILE}.temp ${TARGET_FILE}

		echo "update TAG after: "
		cat ${TARGET_FILE} | grep tag:

		if [ -f ${BUILD_PROPERTIES_FILE} ]; then
		   rm ${BUILD_PROPERTIES_FILE}
		   touch ${BUILD_PROPERTIES_FILE}
		else 
		   touch ${BUILD_PROPERTIES_FILE}  
		fi

		echo "Update ${BUILD_PROPERTIES_FILE} -> IMAGE_TAG: ${IMAGE_TAG}"
		echo "IMAGE_TAG: ${IMAGE_TAG}" > ${BUILD_PROPERTIES_FILE}
		echo "After Update:"
		cat ${BUILD_PROPERTIES_FILE}

		cd ${WORKING_DIR}
		echo "CURRENT Directoy: ${PWD}"

	    echo "---------------------------"
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
			    baseDir: '/var/lib/jenkins/workspace/JenkinsUCDtest_master-GSJH5RUKHTMOJOZ56VZPJHYWVWHRTNGSXAWNZC7U3VUJCVM4XMDQ/chart/jenkinstest',
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
                deployApp: 'JenkinsTestApp',
                deployEnv: 'Dev',
                deployProc: 'Deploy Application',
                deployVersions: 'JenkinsTest:${BUILD_NUMBER}',
                deployOnlyChanged: false
            ]
        ])
		
				
	}     	    

    }
	
