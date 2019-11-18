pipeline {
    agent any
    environment {
		
		GIT_PROJECT_NAME='jenkins-doc'
		
        GCP_DEV_APPLICATION_ACOUNT = ''
        GCP_DEV_PROJECT_ID = '' 
        GCP_DEV_SERVICE_ACCOUNT_KEY=''
		GCP_INSTANCE_PROJECT_DEV=''
		
        GCP_PROD_APPLICATION_ACOUNT = ''
        GCP_PROD_PROJECT_ID = ''
        GCP_PROD_SERVICE_ACCOUNT_KEY=''
		GCP_INSTANCE_PROJECT_PROD=''
        
    }

    stages {
		
		stage ('BUILD DEVELOP BRANCH') {
			agent {
				docker {
					image 'docker.io/google/cloud-sdk'
                    args '-u root:root'
				}
			}
		    when {
                branch 'develop'
		    }

			steps {
                //sh 'gcloud auth configure-docker -q'
                sh 'echo ${GCP_DEV_SERVICE_ACCOUNT_KEY} | base64 -d > auth.json'
				sh 'gcloud config set project ${GCP_DEV_PROJECT_ID}'
				sh 'gcloud auth activate-service-account ${GCP_DEV_APPLICATION_ACOUNT} --key-file=auth.json --project=${GCP_DEV_PROJECT_ID}'
                sh 'gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://gcr.io'
				sh 'docker build --build-arg env_properties=Dockerfile -t gcr.io/${GCP_DEV_PROJECT_ID}/${GIT_PROJECT_NAME}-dev:latest .'
                sh 'docker push gcr.io/${GCP_DEV_PROJECT_ID}/${GIT_PROJECT_NAME}-dev:latest'
            }
        }
		
		
		stage ('BUILD MASTER BRANCH') {
			agent {
				docker {
					image 'docker.io/google/cloud-sdk'
                    args '-u root:root'
				}
			}
		    when {
                branch 'master'
		    }

			steps {
                //sh 'gcloud auth configure-docker -q'
                sh 'echo ${GCP_PROD_SERVICE_ACCOUNT_KEY} | base64 -d > auth.json'
				sh 'gcloud config set project ${GCP_PROD_PROJECT_ID}'
				sh 'gcloud auth activate-service-account ${GCP_PROD_APPLICATION_ACOUNT} --key-file=auth.json --project=${GCP_PROD_PROJECT_ID}'
                sh 'gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://gcr.io'
				sh 'docker build --build-arg env_properties=Dockerfile -t gcr.io/${GCP_PROD_PROJECT_ID}/${GIT_PROJECT_NAME}-prod:latest .'
                sh 'docker push gcr.io/${GCP_PROD_PROJECT_ID}/${GIT_PROJECT_NAME}-prod:latest'
            }
        }
		
		stage ('DEPLOY DEVELOP BRANCH') {
			agent {
				docker {
					image 'docker.io/google/cloud-sdk'
                    args '-u root:root'
				}
			}
		    when {
                branch 'develop'
		    }

			steps {
                
				sh 'echo ${GCP_DEV_SERVICE_ACCOUNT_KEY} | base64 -d > auth.json'
				sh 'gcloud config set project ${GCP_DEV_PROJECT_ID}'
				sh 'gcloud auth activate-service-account ${GCP_DEV_APPLICATION_ACOUNT} --key-file=auth.json --project=${GCP_DEV_PROJECT_ID}'
                sh 'gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://gcr.io'
				sh 'gcloud compute ssh ${GCP_INSTANCE_PROJECT_DEV} --zone us-central1-a  --command "sudo su && echo $GCP_DEV_SERVICE_ACCOUNT_KEY | base64 -d > auth.json && cat  auth.json | docker login -u _json_key --password-stdin https://gcr.io && sudo docker pull gcr.io/${GCP_DEV_PROJECT_ID}/${GIT_PROJECT_NAME}-dev:latest && cd /app && sudo docker-compose up -d --build"'
            }
        }
        

        stage ('DEPLOY MASTER BRANCH') {
			agent {
				docker {
					image 'docker.io/google/cloud-sdk'
                    args '-u root:root'
				}
			}
		    when {
                branch 'master'
		    }

			steps {
                
				sh 'echo ${GCP_PROD_SERVICE_ACCOUNT_KEY} | base64 -d > auth.json'
				sh 'gcloud config set project ${GCP_PROD_PROJECT_ID}'
				sh 'gcloud auth activate-service-account ${GCP_PROD_APPLICATION_ACOUNT} --key-file=auth.json --project=${GCP_PROD_PROJECT_ID}'
                sh 'gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://gcr.io'
				sh 'gcloud compute ssh ${GCP_INSTANCE_PROJECT_PROD} --zone us-central1-a  --command "sudo su && echo $GCP_PROD_SERVICE_ACCOUNT_KEY | base64 -d > auth.json && cat  auth.json | docker login -u _json_key --password-stdin https://gcr.io && sudo docker pull gcr.io/${GCP_PROD_PROJECT_ID}/${GIT_PROJECT_NAME}-dev:latest && cd /app && sudo docker-compose up -d --build"'
            }
        }

    }
}
