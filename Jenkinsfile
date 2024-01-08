node(){
    
    
def containerName="healthcare"
def tag="latest"
def dockerHubUser="bhoisuvarna"
    
    stage('prepare enviroment'){
      
        tagName="latest"
    }
    
    stage('git code checkout'){
        try{
            echo 'checkout the code from git repository'
            git 'https://github.com/suvi98/star-agile-health-care.git'
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
            emailext body: '''Dear All,
            The Jenkins job ${JOB_NAME} has been failed. Request you to please have a look at it immediately by clicking on the below link. 
            ${BUILD_URL}''', subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} is failed', to: 'suvarnabhoi98@gmail.com'
        }
    }
    
    stage('Build the Application'){
        echo "Cleaning... Compiling...Testing... Packaging..."
        //sh 'mvn clean package'
        sh "mvn clean package"        
    }
    
//    stage('publish test reports'){
//        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/InsureMe/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
//    }
     stage("Image Prune"){
         sh "docker image prune -f"
    }

    
 stage('Image Build'){
        sh "docker build -t $containerName:$tag --pull --no-cache ."
        echo "Image build complete"
    }
    

    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'DockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u $dockerUser -p $dockerPassword"
            sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
            sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
            
        }
    }
	 stage("Ansible"){
        ansiblePlaybook credentialsId: 'AnsibleCred', disableHostKeyChecking: true, inventory: 'Inventory', playbook: 'ansible-playbook.yml'
    }
 
 
}
