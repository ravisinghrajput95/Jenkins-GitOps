node {
    def app
    stage('Clone repository') {
       checkout scm
    }

    stage('Build image') {
        app = docker.build("rajputmarch2020/test")
    }

    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'docker') {
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
                    sh "sed -i 's+rajputmarch2020/test.*+rajputmarch2020/test:${env.BUILD_NUMBER}+g' deployment.yaml"
                    sh "cat deployment.yaml"
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/Jenkins-GitOps.git HEAD:main"
                }
            }
        }
    }
}