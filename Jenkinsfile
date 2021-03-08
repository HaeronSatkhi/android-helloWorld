pipeline {
  environment {    
    registry = "localzi-dev"
    registryCredential = 'awscred'
    dockerImage = ''         
    ECRURL = 'https://895767246702.dkr.ecr.ap-south-1.amazonaws.com/localzi-dev'
    ECRCRED = 'ecr:ap-south-1:awscred'   
    Sakthi_UserName =credentials("Localzi_Github_Password")
        KEY_PASSWORD = "keypwd"
        KEY_ALIAS = "androidAlias"
        STORE_PATH = credentials('android_keystore')
        KEYSTORE_PASSWORD = "keystorepwd"
  }
  agent any
  tools{
	   gradle 'GRADLE_HOME'
        }	
  stages {
    stage('Get SCM') {
	   steps{     
        sh 'rm -rf android-helloWorld'
	      sh 'git clone https://github.com/DPMadhavan/android-helloWorld.git'		
      }
     } 
   stage('Setup Tools') {
	   steps {
             withCredentials([file(credentialsId: 'android_keystore', variable: 'KEYFILE')]) {
                 sh "cp \$KEYFILE app/keystore.jks"
             } 
	         } 
         } 
    stage('Build') {
          steps {
		        sh 'gradle clean'
		        sh 'gradle assembleDebug -x test'
		        sh 'gradle build -PstorePass=${KEYSTORE_PASSWORD} -PfilePath=${STORE_PATH} -Palias=${KEY_ALIAS} -PkeyPass=${KEY_PASSWORD}'
		        //sh 'gradle assembleRelease'
           }
        }
    stage('Deploy App to Store') {
       steps {
        echo 'Deploying to PlayStore'
          script {			    		   
	        androidApkUpload googleCredentialsId: 'Google Play Console Developer',
                 filesPattern: '**/build/outputs/**/*.apk',
		 trackName: 'alpha',
		 //trackName: 'internal',
		 //trackName: 'release'
	         rolloutPercentage: '0'                      
		}
	    }
	}
  }
}
