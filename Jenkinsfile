pipeline {
    agent any
    parameters {string(name: 'reponame', defaultValue: '', description: '') }
    tools {
        maven 'Maven'
    }
    stages {
        stage ('incremental version') {
         steps {
             script{
            // def reponame = "samehpalas/demo-app"  //instead, value as an input from the customer as shown above
             def version = "0"
             def tag= "$version-$BUILD_NUMBER" // BUILD_NUMBER of jenkins to increase image dynamically according to build number
             env.imagename = "$reponame:$tag"  // env.xx to be called from another stage
          }
         }
    }
     // stage('buildJAR') {
     //     steps {
     //        echo 'building the Application... '
     //        sh 'mvn package'
    //     }
    //  }
        
        stage('buildIMG') {
            steps {
                echo 'Building the docker image... '
                withCredentials([usernamePassword(credentialsId: 'sameh-dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')])
                 {
                   sh " docker build -t $imagename simplefile_Examples/versioning/ "  //where docker_file located
                   sh " echo $PASS | docker login -u $USER --password-stdin "
                   sh " docker push $imagename "
            }
            }
        }
        
        stage('deploy') {
            steps {
                echo 'Deploying the Application... '
            }
        }
        stage ('modify and push') {
            steps {
              script {  //to increase versionng on github, not just local
               withCredentials([usernamePassword(credentialsId: 'sameh-gihub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')])  //credentials
               sh 'git remote set-url origin https://${USER}:${PASS}@github.com/samehpalas/Jenkins.git'  //pushing to remote repo
               sh 'git config user.name "jenkins"'  // Set the Git user information that will push
               sh 'git config user.email "jenkins@example.com"'
               sh 'git add .'
               sh 'git commit -m 'ci: version commit bump '
               sh 'git push origin HEAD:main'
              } 
            }
        }
    }
}
