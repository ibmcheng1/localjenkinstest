{
    node {
        def gitCommit
	def UCD_DELIVERY_BASE_DIR    
        stage ('Extract') {
          checkout scm
          gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          echo "checked out git commit ${gitCommit}"
        }
	    
        stage ('Push to UCD...') {		
            def imageTag = gitCommit
	    def IMAGE_TAG=gitCommit
	    def OFFSET_DIR="chart/jenkinstest"		
	    def TARGET_FILE="values.yaml"
	    def BUILD_PROPERTIES_FILE="build.properties"
	    def TAG_OLD_String="@@@TAG@@@"
	    def TAG_NEW_String=gitCommit
            UCD_DELIVERY_BASE_DIR=WORKSPACE + "/" + OFFSET_DIR		
            sh """
            #!/bin/bash
	    echo "---------------------------"
	    echo "UCD_DELIVERY_BASE_DIR : ${UCD_DELIVERY_BASE_DIR}"
	    echo "Current Directory:"
	    pwd
	    echo "Current Directory File Structure:"
	    ls -l
            echo "imageTag: ${imageTag}"
            echo "BUILD_NUMBER: ${BUILD_NUMBER}"
	    echo "WORKSPACE: ${WORKSPACE}"
	    echo OFFSET_DIR = ${OFFSET_DIR}
	    echo "---------------------------"	    
	    	cd ./${OFFSET_DIR}	    
		echo IMAGE_TAG = ${IMAGE_TAG}
		echo TARGET_FILE = ${TARGET_FILE}

		echo "update TAG before: "
		cat ${TARGET_FILE} | grep tag:
		
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
		echo "IMAGE_TAG=${IMAGE_TAG}" > ${BUILD_PROPERTIES_FILE}
		echo "After Update:"
		cat ${BUILD_PROPERTIES_FILE}

		cd ${WORKSPACE}
		echo "CURRENT Directoy:"
		pwd
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
			    pushVersion: '${BRANCH_NAME}.${BUILD_NUMBER}',
			    baseDir: '${WORKSPACE}/chart/jenkinstest',
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
                deployVersions: 'JenkinsTest:${BRANCH_NAME}.${BUILD_NUMBER}',
                deployOnlyChanged: false
            ]
        ])
		
				
	}     	    

    }
}	
