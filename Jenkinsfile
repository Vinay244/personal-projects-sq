pipeline {
    /*
    agent {
        node{
            label 'NBU'
            customWorkspace "workspace/${env.JOB_NAME}"
            }
    }
    */
    agent any
    environment {
        //GITHUB_TOKEN = credentials('afdcc8c7-083e-4836-b577-3a24ceaca338')
        GITHUB_TOKEN = credentials('nilart-github')
    }
    options {
        buildDiscarder(logRotator(artifactDaysToKeepStr: '30', artifactNumToKeepStr: '5', daysToKeepStr: '30', numToKeepStr: '5'))
        timestamps()
    }
    tools {
        maven 'maven-3.8.5-auto'
        jdk 'jdk11'
    }
    stages {
        stage('Compile') {
            steps {
                sh "echo hii"
                sh "mvn install"
            }
        }
        
        // Stage for Sonar Scan (placeholder)
        stage('Sonar Scan placeholder') {
            // Commented out steps block to make it visible but not execute any commands
            // steps {
            //     sh "mvn sonar:sonar"
            // }
        }

        stage('Build docker image') {
           steps {
                sh "docker build -t personalproject ."
                sh "docker tag  personalproject shubh9975/personal-project:v6.6.6"
           }
        }

        stage('Docker login and push') {
            steps {
                sh "docker login --username shubh9975 --password $DOCKERHUB_TOKEN"
                sh "docker push shubh9975/personal-project:v6.6.6"
            }
        }
        
        stage('Depoly microservice via k8s yaml on k8s setup via ansible') {
            steps {
                sh "ansible-playbook deployment/tests/test.yml -vvv"
           }
         }
    }
  
    post {
       failure {
           slackSend message:"Build failed  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
        success {
           slackSend message:"Build deployed successfully - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
         }
    }
}
