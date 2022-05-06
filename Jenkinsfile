pipeline {
    agent any
    environment {
        google_key =  credentials('gcp-auth')
     	new_project = credentials('new-gcp-account')
    }
    stages {
        stage('credentials') {
            steps {
              sh''' 
              mkdir -p creds
              echo $google_key | base64 -d > ./creds/creds.json
             '''
            }
        }

    stage ('Configuration_on_old_project'){
            steps {
                sh '''
                 echo $new_project | base64 -d > ./creds/new-gcp-account.json
               gcloud auth activate-service-account $OLD_PROJECT_SA --key-file=./creds/creds.json
	       gcloud config set project $OLD_PROJECT_ID
	       gcloud config set account $OLD_PROJECT_SA
		   '''
                
            }
        }
        
    stage ('Binding_Image_user_to_New_Service_Account'){
            steps {
              sh   'gcloud projects add-iam-policy-binding $OLD_PROJECT_ID --member="serviceAccount:$NEW_SA_ACCOUNT"  --role="$IMAGE_USER_ROLE"'
            }
        }
        
//	stage('Stopping_VM') {
//            steps {
//  	   sh 'gcloud compute instances stop $OLD_VM_NAME --zone=$SOURCE_VM_ZONE  --quiet'
// 	   sh 'sleep 10s'
//                 }
//    }
        stage('Create_Image_from_Old_Project') {
            steps {
     sh 'gcloud compute images create $IMAGE_NAME --project=$OLD_PROJECT_ID --source-disk=$OLD_VM_NAME --source-disk-zone=$SOURCE_VM_ZONE --storage-location=$IMAGE_LOCATION --force'
 
                 }
        
    }
//	stage('Starting_VM') {
//            steps {
//  	   sh 'gcloud compute instances start $OLD_VM_NAME --zone=$SOURCE_VM_ZONE  --quiet'
// 	   sh 'sleep 10s'
//                 }
//    }
    
 
        stage ('Configuration_on_new_Project'){
            steps {
		 sh '''
               gcloud auth activate-service-account $NEW_SA_ACCOUNT --key-file=./creds/new-gcp-account.json
	       gcloud config set project $NEW_PROJECT_ID
	       gcloud config set account $NEW_SA_ACCOUNT
		   '''
              
            }
        }
        
	stage ('Create_new_VM'){
            steps {
		 sh '''
               gcloud compute instances create $NEW_INSTANCE_NAME --project=$NEW_PROJECT_ID --zone=$NEW_VM_ZONES --tags=http-server --image=projects/$OLD_PROJECT_ID/global/images/$IMAGE_NAME
		   '''
              
            }
        }
        
        
}
}
