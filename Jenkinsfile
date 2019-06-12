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
        env.TIMESTAMP = getTimeStamp()
        env.APP_NAME= getEnvVar('APP_NAME')
        env.IMAGE_NAME = getEnvVar('IMAGE_NAME')
        env.REGISTRY_USER = getEnvVar('REGISTRY_USER')
        env.REGISTRY_PWD = getEnvVar('REGISTRY_PWD')
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
          if (env.BRANCH_NAME == "surendra") {
            sh '''
            echo ${CURRENT_BRANCH}
            echo "Setting up Docker registry using ansible playbook"
            #cd docker_registry
            #whoami
            #docker run --entrypoint htpasswd registry:2 -Bbn $REGISTRY_USER $REGISTRY_PWD > roles/provision/files/htpasswd
            #ansible-playbook -i hosts provision.yml -u $REGISTRY_USER --connection=local
            #ansible-playbook -i hosts deploy.yml -u $REGISTRY_USER --connection=local
            '''   
         }
        }
      }
    stage('Build Docker images'){
        steps{
            sh '''
            echo "Build Sample Nginx webserver image"
            #cd nginx
            #docker login --username $REGISTRY_USER --password $REGISTRY_PWD $DOCKER_REGISTRY_URL
            #docker build --force-rm -t $IMAGE_NAME:$BUILD_NUMBER .
            #docker tag $IMAGE_NAME:$BUILD_NUMBER $DOCKER_REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER
            #docker push $DOCKER_REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER
            '''   
    }
  }
}
}
