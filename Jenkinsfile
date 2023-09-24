pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                echo 'Cloning the Project from Github to Jenkins Workspace'
                git 'https://github.com/ersanjaysah/Insurance-CICD.git'
            }
        }
        
        stage('Build the Application'){
            steps {
                echo "Cleaning >> Compiling >> Testing >> Packaging"
                sh "mvn clean package"
            }
        }
        
        stage('HTML Report by TestNG'){
            steps {
                echo "Creating Test Report by testNG"
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/insurance-project/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
        }
        
        stage("Building Docker Image"){
            steps{
                echo "Creating Docker Image"
                sh "docker build -t insuranceimage ."
            }
        }
        
         stage("PUSH IMAGE"){
            steps{
                echo "pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerhub", passwordVariable:"dockerHubPass", usernameVariable:"dockerHubUser")]){
                    sh "docker tag insuranceimage ${env.dockerHubUser}/insuranceimage:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/insuranceimage:latest"
                }
            }
        }
        
        stage("deploy the Application"){
            steps{
                echo "Configuring and deploying the application in the servers"
                ansiblePlaybook credentialsId: 'ssh-key', disableHostKeyChecking: true, installation: 'ansible', playbook: 'ansible_playbook.yml'
            }
        }
    }
}
