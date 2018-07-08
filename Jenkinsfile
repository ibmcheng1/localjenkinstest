pipeline {

    options {
        // Build auto timeout
        timeout(time: 60, unit: 'MINUTES')
    }
    
 	// Some global default variables
    environment {
        IMAGE_NAME = 'acme'
        TEST_LOCAL_PORT = 8817
        DEPLOY_PROD = false
        PARAMETERS_FILE = "${JENKINS_HOME}/parameters.groovy"
        
        gitCommit = null
 		OFFSET_DIR="chart/jenkinstest"		
		TARGET_FILE="values.yaml"
		BUILD_PROPERTIES_FILE="build.properties"
		TAG_OLD_String="@@@TAG@@@"
      
        UCD_APP_NAME = "LocalJenkinsTest"
        UCD_COMPONENT_NAME = "LocalJenkinsTest"
        UCD_COMPONENT_TEMPLATE = "HelmChartTemplate"
        UCD_DELIVERY_BASE_DIR = null
        UCD_DELIVERY_PUSH_VERSION = null       
        UCD_Deploy_Env = "Dev"
        UCD_Deploy_Process = "Deploy"
        UCD_Deploy_Version = null              
    }

    parameters {
        string (name: 'GIT_BRANCH',           defaultValue: 'master',  description: 'Git branch to build')
        booleanParam (name: 'DEPLOY_TO_PROD', defaultValue: false,     description: 'If build and tests are good, proceed and deploy to production without manual approval')


        // The commented out parameters are for optionally using them in the pipeline.
        // In this example, the parameters are loaded from file ${JENKINS_HOME}/parameters.groovy later in the pipeline.
        // The ${JENKINS_HOME}/parameters.groovy can be a mounted secrets file in your Jenkins container.
/*
        string (name: 'DOCKER_REG',       defaultValue: 'docker-artifactory.my',                   description: 'Docker registry')
        string (name: 'DOCKER_TAG',       defaultValue: 'dev',                                     description: 'Docker tag')
        string (name: 'DOCKER_USR',       defaultValue: 'admin',                                   description: 'Your helm repository user')
        string (name: 'DOCKER_PSW',       defaultValue: 'password',                                description: 'Your helm repository password')
        string (name: 'IMG_PULL_SECRET',  defaultValue: 'docker-reg-secret',                       description: 'The Kubernetes secret for the Docker registry (imagePullSecrets)')
        string (name: 'HELM_REPO',        defaultValue: 'https://artifactory.my/artifactory/helm', description: 'Your helm repository')
        string (name: 'HELM_USR',         defaultValue: 'admin',                                   description: 'Your helm repository user')
        string (name: 'HELM_PSW',         defaultValue: 'password',                                description: 'Your helm repository password')
*/
    }
    
    agent any
   
    // Pipeline stages
    stages {
    
    //node {
    //    def gitCommit
		    
        stage ('Extract') {
        	steps {
       			script {
	        		checkout scm
	           		gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
	          		echo "checked out git commit ${gitCommit}"
	          		imageTag = gitCommit
					IMAGE_TAG=gitCommit
	          		TAG_NEW_String=gitCommit 
	          	}       	    
			}
        }
	    
        stage ('Push to UCD...') {
        	steps {
 				script {
					UCD_DELIVERY_BASE_DIR = WORKSPACE + "/" + OFFSET_DIR
					UCD_DELIVERY_PUSH_VERSION = BRANCH_NAME + "." + BUILD_NUMBER
					UCD_Deploy_Version = UCD_COMPONENT_NAME + ":" + BRANCH_NAME + "." + BUILD_NUMBER
				    echo "-------------------------"
				    echo "Environment Information: "
				    echo "-------------------------"
	          		echo "imageTag = ${imageTag}" 
	          		echo "IMAGE_TAG = ${IMAGE_TAG}"
	          		echo "TAG_NEW_String = ${TAG_NEW_String}"
	          		echo "WORKSPACE = ${WORKSPACE}"
	          		echo "UCD_APP_NAME = ${UCD_APP_NAME}"
	          		echo "UCD_COMPONENT_NAME = ${UCD_COMPONENT_NAME}"
	          		echo "UCD_Deploy_Env = ${UCD_Deploy_Env}"
	          		echo "UCD_DELIVERY_BASE_DIR = ${UCD_DELIVERY_BASE_DIR}"
	          		echo "UCD_DELIVERY_PUSH_VERSION = ${UCD_DELIVERY_PUSH_VERSION}"
	          		echo "UCD_Deploy_Process = ${UCD_Deploy_Process}"
	          		echo "UCD_Deploy_Version = ${UCD_Deploy_Version}"
	          		echo "-------------------------"
	          		
	          		sh """
            		#!/bin/bash		
					echo "update TAG before: "
					cat ${TARGET_FILE} | grep tag:
						          		
	          		sed -i "s/@@@TAG@@@:${imageTag}/g" ${TARGET_FILE}
	          		
					echo "update TAG before: "
					cat ${TARGET_FILE} | grep tag:	          		
	          		"""
	          		echo "-------------------------"
	          	} 
 
			    step([$class: 'UCDeployPublisher',
			            siteName: 'UCD-Server',
			            component: [
			                $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
			                componentName: "${UCD_COMPONENT_NAME}",
			                createComponent: [
			                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
			                    componentTemplate: "${UCD_COMPONENT_TEMPLATE}",
			               	    componentApplication: "${UCD_APP_NAME}"
			            	],
			            	delivery: [
			                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
					    		pushVersion: "${UCD_DELIVERY_PUSH_VERSION}",
					    		//baseDir: '/var/lib/jenkins/workspace/JenkinsUCDtest_master-GSJH5RUKHTMOJOZ56VZPJHYWVWHRTNGSXAWNZC7U3VUJCVM4XMDQ/chart/jenkinstest',					    		
					    		baseDir: "${UCD_DELIVERY_BASE_DIR}",
		                	
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
                		deployApp: "${UCD_APP_NAME}",
                		deployEnv: "${UCD_Deploy_Env}",
                		deployProc: "${UCD_Deploy_Process}",
                		deployVersions: "${UCD_Deploy_Version}",
                		deployOnlyChanged: false
            		]
        		])
			    		         	    
        	}
        					
		}
	     	    

    }
}