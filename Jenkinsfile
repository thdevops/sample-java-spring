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
                sh 'cf push thdevops-test -p ./target/springboot-appengine-standard-0.0.1-SNAPSHOT.war'
                // pushToCloudFoundry(
                //     target: 'api.run.pivotal.io',
                //     organization: 'aurelien',
                //     cloudSpace: 'development',
                //     credentialsId: 'pcfdev_user',
                //     selfSigned: 'true',
                //     manifestChoice: [
                //         value: 'jenkinsConfig',
                //         appName: 'thdevops-test',
                //         memory: 128,
                //         instances: 1,
                //         appPath: 'target/springboot-appengine-standard-0.0.1-SNAPSHOT.war'
                //     ]
                // )
            }
        }
    }
}