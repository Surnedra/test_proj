#!/usr/bin/env groovy

def getTimeStamp(){
    return sh (script: "date +'%Y%m%d%H%M%S%N' | sed 's/[0-9][0-9][0-9][0-9][0-9][0-9]\$//g'", returnStdout: true);
}

def getEnvVar(String paramName){
    return sh (script: "grep '${paramName}' env_vars/project.properties|cut -d'=' -f2", returnStdout: true).trim();
}

pipeline{

environment {
    GIT_COMMIT_SHORT_HASH = sh (script: "git rev-parse --short HEAD", returnStdout: true)
}

agent any

stages{
    stage('Init'){
        steps{
            //checkout scm;
        script{
        env.BASE_DIR = pwd()
        env.CURRENT_BRANCH = env.BRANCH_NAME
        env.IMAGE_TAG = getImageTag(env.CURRENT_BRANCH)
        env.TIMESTAMP = getTimeStamp()
        env.APP_NAME= getEnvVar('APP_NAME')
        env.IMAGE_NAME = getEnvVar('IMAGE_NAME')
        env.PROJECT_NAME=getEnvVar('PROJECT_NAME')
        env.DOCKER_REGISTRY_URL=getEnvVar('DOCKER_REGISTRY_URL')
        env.RELEASE_TAG = getEnvVar('RELEASE_TAG')
        env.DOCKER_PROJECT_NAMESPACE = getEnvVar('DOCKER_PROJECT_NAMESPACE')
        env.DOCKER_IMAGE_TAG= "${DOCKER_REGISTRY_URL}/${DOCKER_PROJECT_NAMESPACE}/${APP_NAME}:${RELEASE_TAG}"
        env.JENKINS_DOCKER_CREDENTIALS_ID = getEnvVar('JENKINS_DOCKER_CREDENTIALS_ID')        
        env.JENKINS_AWS_CRED_ID = getEnvVar('JENKINS_AWS_CRED_ID')
        env.AWS_PROJECT_ID = getEnvVar('AWS_PROJECT_ID')
        env.AWS_K8S_CLUSTER_NAME = getEnvVar('AWS_K8S_CLUSTER_NAME')
        env.JENKINS_AWS_CRED_LOCATION = getEnvVar('JENKINS_AWS_CRED_LOCATION')

        }

        }
    }

    stage('Setup Docker Registry'){
        steps{
            sh '''
            echo "Setting up Docker registry using ansible playbook"
            cd ${env.BASE_DIR}/
            '''
        }

    }
    /*
    stage('Cleanup'){
        steps{
            sh '''
            #docker rmi $(docker images -f 'dangling=true' -q) || true
            #docker rmi $(docker images | sed 1,2d | awk '{print $3}') || true
            echo "Skip..."
            '''
        }

    }
    stage('Build'){
        steps{
            withEnv(["APP_NAME=${APP_NAME}", "PROJECT_NAME=${PROJECT_NAME}"]){
                sh '''
                docker build -t ${DOCKER_REGISTRY_URL}/${DOCKER_PROJECT_NAMESPACE}/${IMAGE_NAME}:${RELEASE_TAG} --build-arg APP_NAME=${IMAGE_NAME}  -f app/Dockerfile app/.
                '''
            }   
        }
    }
    stage('Publish'){
        steps{
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "${JENKINS_DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWD']])
            {
            sh '''
            echo $DOCKER_PASSWD | docker login --username ${DOCKER_USERNAME} --password-stdin ${DOCKER_REGISTRY_URL} 
            #docker push ${DOCKER_REGISTRY_URL}/${DOCKER_PROJECT_NAMESPACE}/${IMAGE_NAME}:${RELEASE_TAG}
            docker push 35.247.3.95:8083/${DOCKER_PROJECT_NAMESPACE}/${IMAGE_NAME}:${RELEASE_TAG}
            docker logout
            '''
            }
        }
    }
    //stage('Deploy'){
    //    steps{
    //   withCredentials([file(credentialsId: "${JENKINS_GCLOUD_CRED_ID}", variable: 'JENKINSGCLOUDCREDENTIAL')])
    //    {
    //    sh """
    //        gcloud auth activate-service-account --key-file=${JENKINSGCLOUDCREDENTIAL}
    //        gcloud config set compute/zone asia-southeast1-a
    //        gcloud config set compute/region asia-southeast1
    //        gcloud config set project ${GCLOUD_PROJECT_ID}
    //        gcloud container clusters get-credentials ${GCLOUD_K8S_CLUSTER_NAME}
            
    //        chmod +x $BASE_DIR/k8s/process_files.sh

    //       cd $BASE_DIR/k8s/
    //        ./process_files.sh "$GCLOUD_PROJECT_ID" "${IMAGE_NAME}" "${DOCKER_PROJECT_NAMESPACE}/${IMAGE_NAME}:${RELEASE_TAG}" "./${IMAGE_NAME}/" ${TIMESTAMP}

    //        cd $BASE_DIR/k8s/${IMAGE_NAME}/.
    //       kubectl apply -f $BASE_DIR/k8s/${IMAGE_NAME}/
    //        kubectl rollout status --v=5 --watch=true -f $BASE_DIR/k8s/$IMAGE_NAME/$IMAGE_NAME-deployment.yml
            
     //        gcloud auth revoke --all
     //       """
     //   }
     //   }
    //}
}*/
post {
    always {
        echo "Build# ${env.BUILD_NUMBER} - Job: ${env.JOB_NUMBER} status is: ${currentBuild.currentResult}"
    }
}
}
