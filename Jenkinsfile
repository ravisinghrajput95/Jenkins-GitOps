node {
    def app
    environment {
        DOCKERTAG = "$env.buildnumber"
    }

    stage('Clone repository') {
       checkout scm
    }

    stage('Build image') {
        app = docker.build("raj80dockerid/test")
    }

    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
        app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('ManifestUpdate') {
        echo "Update Manifest"
        script{
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                 withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {                              
                    sh "git config user.email ravisinghrajput005@gmail.com"
                    sh "git config user.name ravisinghrajput95"
                    sh "cat deployment.yaml"
                    sh "sed -i 's+rajputmarch2020/test.*+rajputmarch2020/test:${DOCKERTAG}+g' deployment.yaml"
                    sh "cat deployment.yaml"
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                }
            }
        }
    }
}