pipeline {
    agent any
    tools {nodejs "Node"}
    environment {
        CI = 'true'
        API_DIR = '/var/jenkins_home/workspace/NITAMTNDeployment'
        DEV_ENV = 'nita-dev'
		HOST_ENV = 'nita-host'
    }
    stages {
        stage('Preparation') {
            steps{
                git branch: "master",
                url: 'http://192.168.0.82:4000/nickson/nita-mtn-push.git',
                credentialsId: 'root'
            }
        }
        stage('Deploy to Nita Host') {
            environment{
                RETRY = '80'
            }
            steps {
             echo 'Create a nita-dev environment'
                sh '${CTL_HOME} list envs'
                sh '${CTL_HOME} remove env  nita-host'
                sh '${CTL_HOME} add-env -e  $HOST_ENV --apim https://192.168.0.113:9443'
                echo '--------------------Logging into $HOST_ENV----------------'
				withCredentials([usernamePassword(credentialsId: 'apim_dev', usernameVariable: 'DEV_USERNAME', passwordVariable: 'DEV_PASSWORD')]) {
                    sh '${CTL_HOME} login $HOST_ENV -u $DEV_USERNAME -p $DEV_PASSWORD -k'                        
                }   
                echo '------------------------Deploying to $HOST_ENV-----------------------'
                sh '${CTL_HOME} import-api -f $API_DIR -e $HOST_ENV -k --preserve-provider --update --verbose'
            }
        }
    }
}
