pipeline {
    agent none
    environment {
        JAVA_HOME = '/usr/bin/java'
    }
    stages {
        stage('Build') {
            agent {
                label 'Node'
            }
            steps {
                echo 'Building the application'
                // Define build steps here
                sh '/opt/maven/bin/mvn clean package'
            }
        }
        stage('Test') {
            agent {
                label 'Node'
            }
            steps {
                echo 'Running tests'
                // Define test steps here
                sh 'mvn test'
                stash(name: 'Jenkins-ci-cd-project', includes: 'target/*.war')
            }
        }
        stage('Deploy') {
            agent {
                label 'Node2'
            }
            steps {
                echo 'Deploying the application'
                // Define deployment steps here
                unstash 'Jenkins-ci-cd-project'
                sh "~/apache-tomcat-7.0.94/bin/startup.sh"
                sh "sudo rm -rf ~/apache*/webapps/*war"
                sh "sudo mv target/*.war ~/apache/webapps/"
                sh "sudo systemctl daemon-reload"
                // sh "~/apache-tomcat-7.0.94/bin/shutdown.sh"
                sh "~/apache-tomcat-7.0.94/bin/startup.sh"
            }
        }
    }
    post {
        success {
            script {
                // Send email for a successful build
                mail to: 'kelvinatete@yahoo.com',
                     subject: "Build Successful - ${currentBuild.fullDisplayName}",
                     body: "Congratulations! The build was successful.\n\nCheck console output at ${BUILD_URL}"
            }
        }
        failure {
            script {
                // Send email for a failed build
                mail to: 'kelvinatete@yahoo.com',
                subject: "Build Failed - ${currentBuild.fullDisplayName}",
                body: "Oops! The build failed.\n\nCheck console output at ${BUILD_URL}"
            }    
        }
    }
}
