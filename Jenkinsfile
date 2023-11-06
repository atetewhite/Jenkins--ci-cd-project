pipeline {
    agent any
    environment {
        JAVA_HOME = '/usr'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building the application'
                // Define build steps here
                sh '/opt/maven/bin/mvn clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests'
                // Define test steps here
                sh 'mvn test'
                stash(name: 'Jenkins-ci-cd-project', includes: 'target/*.war')
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying the application'
                // Define deployment steps here
                unstash 'Jenkins-ci-cd-project'
                sh "~/apache-tomcat-7.0.94/bin/startup.sh"
                sh "sudo rm -rf ~/apache-tomcat-7.0.94/webapps/*.war"  // Fix the path
                sh "sudo mv target/*.war ~/apache-tomcat-7.0.94/webapps/"  // Fix the path
                sh "sudo systemctl daemon-reload"
            }
        }
    }
    post {
        success {
            script {
                // Send email for a successful build
                emailext subject: "Build Successful - ${currentBuild.fullDisplayName}",
                          body: "Congratulations! The build was successful.\n\nCheck console output at ${BUILD_URL}",
                          to: 'kelvinatete@yahoo.com'
            }
        }
        failure {
            script {
                // Send email for a failed build
                emailext subject: "Build Failed - ${currentBuild.fullDisplayName}",
                          body: "Oops! The build failed.\n\nCheck console output at ${BUILD_URL}",
                          to: 'kelvinatete@yahoo.com'
            }    
        }
    }
}
