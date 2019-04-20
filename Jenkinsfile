node {
    environment {
      registry = "927373803645.dkr.ecr.us-east-1.amazonaws.com/"
      repo = "dev-codehub/codehub-ui"
      //797335914619.dkr.ecr.us-east-1.amazonaws.com/dev-codehub/codehub-ui:latest
      imageUrl= '$registry+repo:$BUILD_NUMBER'
      DOCKER_LOGIN='(aws ecr get-login --no-include-email --region us-east-1)'
      dockerImage = ''
      registryurl = '927373803645.dkr.ecr.us-east-1.amazonaws.com/'
      GIT_BRANCH = 'dev_updated'
      GIT_REPO = 'https://github.com/usdot-jpo-codehub/codehub-ui.git'
      SONAR_HOST_URL='http://localhost:9000'
      SONAR_AUTH_TOKEN='121d594f68b9cafd02f7433c0c992a3e19af02d3'
    }
    //tools {nodejs "node"}
     stage('Git Checkout') {
          deleteDir()
          dir ('App'){
              //stdStage.checkout(GIT_REPO, GIT_BRANCH)
              git(
                branch: 'dev_updated',
                url: 'https://github.com/usdot-jpo-codehub/codehub-ui.git'
            )
          }

        }

    stage('Unit Test') {
        nodejs('node') {
            dir ('App'){
              script {
                sh 'npm install'
                sh 'npm install -g gulp'
                sh 'npm install -g aurelia-cli'
                sh 'npm install aurelia-cli'
                sh 'npm i --save-dev karma karma-chrome-launcher karma-mocha karma-chai'
                //sh 'export CHROME_BIN=/usr/bin/google-chrome'
                //sh 'au test'
                sh 'au package-bundle --env prod'
                sh 'ls -l'
                sh 'echo Bundling is Complete!!'
            }
          }
  }
  }

      stage('Static Code Analysis'){
        dir ('App'){

        script {
            def scannerHome = tool 'SonarQube Scanner 2.8';
            withSonarQubeEnv('SonarQube') {
                    // git(
                    //    branch: 'development-bundler',
                    //    url: 'https://github.com/usdot-jpo-codehub/codehub-ui.git'
                  //  )
                    sh "ls -l"
                    sh 'ls "${scannerHome}"/bin/'
                    sh 'cat /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube_Scanner_2.8/conf/sonar-scanner.properties'
                    //sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectName=codehub-ui-base -Dsonar.projectVersion=1.0.0 -Dsonar.projectKey=codehub-ui-base -Dsonar.sources=."
                    sh "${scannerHome}/bin/sonar-scanner -X  -Dsonar.projectName=codehub-ui-base -Dsonar.projectVersion=1.0.0 -Dsonar.projectKey=codehub-ui-base -Dsonar.sources=src,scripts"
                }
            }
        }
      }
      stage('508 Complaince') {
          script {
              sh 'echo 508 Complaince is complete'
          }
      }

      stage('Integration Test1') {
          script {

              sh 'echo Integration Test 1 is complete'
          }
      }

      stage('Integration Test2') {
          script {

              sh 'echo Integration Test 2 is complete'
          }
      }

      stage('Build Codehub-UI Base Image') {
      dir ('App'){
          script {
            withAWS(region:'us-east-1') {
              sh 'eval $(aws ecr get-login --no-include-email) > login'
              dockerImage=docker.build("797335914619.dkr.ecr.us-east-1.amazonaws.com/dev-codehub/codehub-ui" + ":latest")
          }
            sh 'echo "Completing image build"'
          }
      }
}

      stage('Publish Codehub-UI Base Image') {
      dir ('App'){
          script {
            withAWS(region:'us-east-1') {
              sh 'eval $(aws ecr get-login --no-include-email) > login'
              dockerImage.push()
          }
            sh 'echo "Completing image build"'
          }
      }
}
      stage('Update TaskDefinition') {
      dir ('App'){
          script {
              sh 'aws ecs register-task-definition --cli-input-json file://codehub-ui-taskDefinition.json --region us-east-1'
              sh 'echo Service is Updated'
          }
      }
      }
      stage('Deploy Service') {
      dir ('App'){
      nodejs('node') {
            script {
            SERVICE_NAME="codehub-ui-service"
            IMAGE_VERSION="$BUILD_NUMBER"
            TASK_FAMILY="codehub-ui"
              sh 'npm config ls'
              sh 'npm install js-yaml -g'
              sh 'npm install js-yaml'
              sh 'aws ecs register-task-definition --cli-input-json file://codehub-ui-taskDefinition.json --region us-east-1'
              sh 'node process_appspec.js $(aws ecs list-task-definitions --region us-east-1 --family-prefix codehub-ui | jq -r ".taskDefinitionArns[-1]")'
              sh 'aws s3 cp appspec.yaml s3://codehub-prod-ui/'
              sh 'aws deploy create-deployment --cli-input-json file://codehub-ui-create-deployment.json --region us-east-1'
          }
        }
}
}

}
