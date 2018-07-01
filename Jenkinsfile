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
        
        UCD_DELIVERY_BASE_DIR = null
        gitCommit = null
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
	          	}       	    
			}
        }
	    
        stage ('Push to UCD...') {
        	steps {
        		step {
        		    def imageTag = gitCommit
				    def IMAGE_TAG=gitCommit
				    def OFFSET_DIR="chart/jenkinstest"		
				    def TARGET_FILE="values.yaml"
				    def BUILD_PROPERTIES_FILE="build.properties"
				    def TAG_OLD_String="@@@TAG@@@"
				    def TAG_NEW_String=gitCommit
				    UCD_DELIVERY_BASE_DIR=WORKSPACE + "/" + OFFSET_DIR      		    
        		}
        		
				script {
				    echo "-------------------------"
	          		echo "UCD_DELIVERY_BASE_DIR = " + ${UCD_DELIVERY_BASE_DIR}
	          		echo "-------------------------"
	          	} 
 
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
					    		//baseDir: '/var/lib/jenkins/workspace/JenkinsUCDtest_master-GSJH5RUKHTMOJOZ56VZPJHYWVWHRTNGSXAWNZC7U3VUJCVM4XMDQ/chart/jenkinstest',
					    		
					    		baseDir: '${WORKSPACE}',
		                	
			                	fileIncludePatterns: '/**',
			                	fileExcludePatterns: '',
			                  
			                	pushDescription: 'Pushed from Jenkins',
			                	pushIncremental: false
			                ]			    
			            ]
			     ])     	    
        	}
        					
		}
	     	    

    }
}