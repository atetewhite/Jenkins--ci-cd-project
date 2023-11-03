pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                label 'Node1'
            }
            steps {
                echo 'Building the application'
                // Define build steps here
                sh '/opt/maven/bin/mvn clean package'
            }
        }
        stage('Test') {
            agent {
                label 'Node1'
            }
            steps {
                echo 'Running tests'
                // Define test steps here
                sh 'mvn test'
                stash (name: 'jenkinsProject', includes: "target/*war")
            }
        }
        stage('Deploy') {
            agent {
                label "Node1"
            }
            steps {
                echo 'Deploying the application'
                // Define deployment steps here
                unstash 'JenkinsProject'
                sh "~/apache-tomcat-7.0.94/bin/startup.sh"
                sh "sudo rm -rf ~/apache*/webapp/*war"
                sh "sudo mv target/.war ~/apache/webapps/"
                sh "sudo systemctl daemon-reload"
                // sh "~/apache-tomcat-7.0.94/bin/shutdown.sh"
                sh "~/apache-tomcat-7.0.94/bin/startup.sh"
            }
        } 
    }
    post {
        success {
            script {
                //Send email for successful build
                mail to: 'kelvinatete@yahoo.com,'
                     subject: "Build Successful - ${currentBuild.fullDisplayName}",
                     body: "Congratulations! The build was successful.\n\nCheck console output at ${Build_URL}"
            }
        }
        failure {
            script {
                // Send email for failed build
                mail to: 'kelvinatete@yahoo.com',
                subject: "Build Failed - ${ currentBuild.fullDisplayName}",
                body: "Oops! The build failed.\n\nCheck console output at ${Build_URL}"
            }    
        }

    }
