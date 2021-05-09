pipeline {
    agent any
    stages {
        stage("Env Variables") {
            steps {

                script {
                    //env.ECRREPOURI = "565057454984.dkr.ecr.ap-south-1.amazonaws.com/med-admin"
                    //env.DOCKERPUSHURL = "https://565057454984.dkr.ecr.ap-south-1.amazonaws.com/med-admin"
                    registry = "srikanth437/test-deploy"
                    registryCredential = 'dockerhub-creds'
                    env.TAG = "test-" + "${BUILD_NUMBER}"
                    env.IMAGE = "srikanth437/test-deploy" + ":" + "${env.TAG}"
                    env.FILENAME = "deployment-test.yaml"
                } //script end           
            } //steps end
        } // env Variables stage end
        
        stage("Docker build") {
            steps {
                sh """ 
                    docker build . -t ${env.IMAGE}
                """
            } // steps end
        } // docker build stage end

        stage("Docker push ") {
            steps
              {
                script { 

                    //docker.withRegistry( 'https://registry.hub.docker.com' , registryCredential ) { 
                        withDockerRegistry([ credentialsId: "dockerhub-creds", url: "" ]) {
                        sh "docker push ${env.IMAGE}"
                    }
                }
            } // steps end
        } //  docker push stage end

        stage("Replacing Yaml Variables") {
            steps {
                sh """      
                    sed -i "s|CONTAINER_IMAGE|${env.IMAGE}|g" ${env.FILENAME}
                """
            } //steps end
        } //Replacing yaml Variables stage end

        stage("k8S file deployment") {
            steps {
               script{                 
                        withKubeConfig(caCertificate: '', clusterName: 'test-development', contextName: '', credentialsId: 'kubernetes-service-account-token', namespace: '', serverUrl: 'https://A5883926147CA7BC33F63A3D3EBA596E.gr7.us-east-2.eks.amazonaws.com') {
                        //sh "kubectl apply -f ${WORKSPACE}/${env.FILENAME}"
                        sh "kubectl apply -f ${env.FILENAME} -n default"
                        sh "kubectl get pods -n default"
                     }  // withKubeConfig end 
                    
                } //script end
            } // steps end
        } //  k8S deployment file deployment stage end
    } //stages end
} //pipeline end
