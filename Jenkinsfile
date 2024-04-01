pipeline {
    agent any
   // parameters {string(name: 'reponame', defaultValue: '', description: '') }
    //tools {
      //  maven 'Maven'
    //}
    stages {
        stage('Clone repository') {  
         steps {
          checkout scm
           }
         }
        stage ('incremental version') {
         steps {
            script{
                
             env.GIT_COMMIT_REV = sh (script: 'git log -n 1 --pretty=format:"%h"', returnStdout: true)
           //  env.GIT_COMMIT_REV = sh (git log  | grep ^commit  |  awk '{print $2}' | cut -c -8)
            // def reponame = "samehpalas/demo-app"  //instead, value as an input from the customer as shown above
             def tag= "$GIT_COMMIT_REV-$BUILD_NUMBER" // BUILD_NUMBER of jenkins to increase image dynamically according to build number
             env.imagename = "samehpalas/test1:$GIT_COMMIT_REV"  // env.xx to be called from another stage
          }
         }
    }
        
        stage('buildIMG') {
            steps {
                echo 'Building the docker image... '
                withCredentials([usernamePassword(credentialsId: 'sameh-dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')])
                 {
                   sh " docker build -t $imagename . "  //where docker_file located
                   sh " echo $PASS | docker login -u $USER --password-stdin "
                   sh " docker push $imagename "
            }
            }
        }
        stage('Trigger CD job ') {
                steps {
                echo "triggering CD"
                build job: 'CD', parameters: [string(name: 'GIT_COMMIT_REV', value: env.GIT_COMMIT_REV)]
        }
        }
        
        //stage ('modify and push') {
          //  steps {
            //  script {  //to increase versionng on github, not just local
              // withCredentials([usernamePassword(credentialsId: 'sameh-gihub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')])  //credentials
               //sh 'git remote set-url origin https://${USER}:${PASS}@github.com/samehpalas/Jenkins.git'  //pushing to remote repo
               //sh 'git config user.name "jenkins"'  // Set the Git user information that will push
               //sh 'git config user.email "jenkins@example.com"'
               //sh 'git add .'
               //sh 'git commit -m 'ci: version commit bump '
               //sh 'git push origin HEAD:main'
              //} 
            //}
        //}
    }
}
