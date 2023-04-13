pipeline {

	agent any

	environment{

		DOCKERHUB_USERNAME = "leegodden"
		APP_NAME = "gitops-argo-app"
		IMAGE_TAG = "${BUILD_NUMBER}"
		IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
		REGISTRY_CREDS = 'dockerhub'
	
	}

	stages{

		stage('Clean up workspace'){

			steps{
				script{
					
					cleanWs()
				}
			}
		}
		stage('Checkout SCM'){

			steps{
				script{
					git credentialsId: 'github'
					url: 'https://github.com/leegodden/gitops_argocd_project.git'
					git branch: 'main'
				}
			}
		}
	}
}