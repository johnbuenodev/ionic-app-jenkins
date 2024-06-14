pipeline {

    agent {
        docker {
            image 'androidsdk/android-30'
        }
    }
    /* agent { label 'mac' } */

    environment {
        branch = 'main'
        url = 'https://github.com/johnbuenodev/curso-ionic-6'
    }

    stages {

        stage('Checkout git') {
            steps {
                git branch: branch, credentialsId: 'ionic6', url: url
            }
        }

        /* stage('Lint') {
            steps {
                sh "./gradlew lint"
            }
        }

        stage('Test') {
            steps {
                sh "./gradlew test --stacktrace"
            }
        } */

        // Manage Jenkins > Credentials > Add > Secret file or Secret Text
        stage('Credentials') {
            steps {
                withCredentials([file(credentialsId: 'ANDROID_KEYSTORE_FILE', variable: 'ANDROID_KEYSTORE_FILE')]) {
                    sh "cp '${ANDROID_KEYSTORE_FILE}' android/app/tasks.jks"
                    sh "cat android/app/hello.jks"
                }
                withCredentials([file(credentialsId: 'FIREBASE_SERVICE_ACCOUNT_FILE', variable: 'FIREBASE_SERVICE_ACCOUNT_FILE')]) {
                    sh "cp '${FIREBASE_SERVICE_ACCOUNT_FILE}' android/app/service-account-firebasedist.json"
                    sh "cat android/app/service-account-firebasedist.json"
                }
            }
        }

        stage('Build') {
            steps {
                sh "cd android && ./gradlew clean assembleRelease"
            }
        }

        stage('Publish') {
            /* parallel { */
                stage('Firebase Distribution') {
                    steps {
					    sh "cd android && ./gradlew appDistributionUploadRelease"
                        /* sh "./gradlew appDistributionUploadRelease" */
                    }
                }

                /*
                stage('Google Play...') {
                    steps {
                        sh "echo 'Test...'"
                    }
                } */
            /* } */
        } 
    }

    post {
       always {
           sh "rm android/app/hello.jks"
           sh "rm android/app/service-account-firebasedist.json"
       }
    }

}