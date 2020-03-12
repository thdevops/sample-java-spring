pipeline {
    agent {
        //kubernetes {
        //    label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
        //    idleMinutes 5  // how long the pod will live after no jobs have run on it
        //    yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
        //    defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
        //}
        docker {
            image 'tenjaa/maven-cf'
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn package' 
                stash name: 'maven_build'
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
                withCredentials([usernamePassword(credentialsId: 'pcfdev_user', usernameVariable: 'username', passwordVariable: 'password')]) {
                    sh '''
                        CF_HOME=$(pwd) cf login -a api.run.pivotal.io -u \"${username}\" -p \"${password}\" -o aurelien -s development
                        CF_HOME=$(pwd) cf push thdevops-test -m 512M -p ./target/springboot-appengine-standard-0.0.1-SNAPSHOT.war
                    '''
                }
            }
        }
    }
}