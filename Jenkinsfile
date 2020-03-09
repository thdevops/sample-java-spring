pipeline {
    agent {
        //kubernetes {
        //    label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
        //    idleMinutes 5  // how long the pod will live after no jobs have run on it
        //    yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
        //    defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
        //}
        docker {
            image 'maven:3-jdk-8'
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn package' 
                stash name: 'maven_build', includes: 'target/**'
            }
        }

        stage('Test') {
            steps {
                unstash 'maven_build'
                sh 'mvn verify'
            }
        }

        stage('Deploy') {
            when {
                branch 'master'
            }

            steps {
                unstash 'maven_build'
                sh 'ls /var/lib/cloudbees-jenkins-distribution/workspace/devops_sample-java-spring_master/target/appengine-staging'
                sh 'cat /var/lib/cloudbees-jenkins-distribution/workspace/devops_sample-java-spring_master/target/appengine-staging/app.yaml'
                sh 'mvn appengine:deploy'
            }
        }
    }
}