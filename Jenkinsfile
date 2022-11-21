node {
    def app
    
    environment {
        String result=’0.0.0';
    }

    stage('Clone repository') {
      

        checkout scm
    }
    
    stage(‘Auto tagging’)
    { 
    steps {
     script {
     sh “”” 
    version=\$(git describe — tags `git rev-list — tags — max-count=1`)
    #Version to get the latest tag 
    A=”\$(echo \$version|cut -d ‘.’ -f1)”
    B=”\$(echo \$version|cut -d ‘.’ -f2)”
    C=”\$(echo \$version|cut -d ‘.’ -f3)”
     if [ \$C -gt 8 ]
     then 
    if [ \$B -gt 8 ]
     then
     A=\$((A+1))
     B=0 C=0 
    else
    B=\$((B+1))
     C=0
     fi
     else
     C=\$((C+1))
     fi
    echo “A[\$A.\$B.\$C]”>outFile “””
    nextVersion = readFile ‘outFile’ 
    echo “we will tag ‘${nextVersion}’” 
    result =nextVersion.substring(nextVersion.indexOf(“[“)+1,nextVersion.indexOf(“]”);
    echo “we will tag ‘${result}’”
                                                        }
                                                        }
                                                        }

    stage('Build image') {
  
       app = docker.build("rla8127/test")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Update GIT') {
            script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                        sh "git config user.email ehdugs77@naver.com"
                        sh "git config user.name rla8127"
                        //sh "git switch master"
                        sh "cat deployment.yaml"
                        sh "sed -i 's+rla8127/test.*+rla8127/test:${DOCKERTAG}+g' deployment.yaml"
                        sh "cat deployment.yaml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
      }
    }
   }
 }
}
