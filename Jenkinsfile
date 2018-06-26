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
         
        stage ('deploy') {
          container('kubectl') {
            def imageTag = null
            imageTag = gitCommit
            sh """
            #!/bin/bash
            echo "checking if jenkinstest-deployment already exists"
            if kubectl describe deployment jenkinstest-deployment --namespace jenkinstest; then
                echo "Application already exists, update..."
                kubectl set image deployment/jenkinstest-deployment jenkinstest=mycluster.icp:8500/jenkinstest/jenkinstest:${imageTag} --namespace jenkinstest
            else
                sed -i "s/<DOCKER_IMAGE>/jenkinstest:${imageTag}/g" manifests/kube.deploy.yml
                echo "Create deployment"
                kubectl apply -f manifests/kube.deploy.yml --namespace jenkinstest
                echo "Create service"
            fi
            echo "Describe deployment"
            kubectl describe deployment jenkinstest-deployment --namespace jenkinstest
            echo "finished"
            """
          }
        }
        
        stage ('Push to UCD...') {
       		step([$class: 'UCDeployPublisher',
	            siteName: 'Docker IBM UrbanCode Deploy Server',
	            component: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	                componentName: 'UCD - Pipeline',
	                createComponent: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock'
	                ],
	                delivery: [
	                    $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                    pushVersion: '${BUILD_NUMBER}',
	                    baseDir: '.',
	                    fileIncludePatterns: '/var/jenkins_home/workspace/datapower-pipeline/dist/*',
	                    fileExcludePatterns: '',
	                    pushProperties: 'jenkins.server=Local\njenkins.reviewed=false',
	                    pushDescription: 'Pushed from Jenkins',
	                    pushIncremental: false
	                ]
	            ]
        	])
   		}       
    }
}
