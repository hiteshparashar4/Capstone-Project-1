# Capstone-Project-1

## Create ECR private registry
<img width="1609" height="251" alt="image" src="https://github.com/user-attachments/assets/3d4d95ba-eb53-45a8-8c08-d2ff9f491bd1" />  
<br /><br /><br />


## Unnecessary - ignore for this project - still committing for future reference
- login to registry using aws cli `aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 407247182709.dkr.ecr.ap-south-1.amazonaws.com`
- tag image `docker tag my-app:1.0 407247182709.dkr.ecr.ap-south-1.amazonaws.com/my-app:1.0`
<br /><br /><br />

## Create k8s cluster using `eksctl`
- make sure aws admin creds are configured
- this will also setup `kubeconfig`
```
eksctl create cluster \
—name demo-cluster \
—version 1.27 \
—region eu-central-1 \
—nodegroup-name demo-nodes \
—node-type t2.micro \
—nodes 2 \
—nodes-min 1 \
—nodes-max 3
```


## Check nodes
<img width="1182" height="101" alt="image" src="https://github.com/user-attachments/assets/28b850e2-6a5a-4a4d-a423-0155433143ff" />



## Create jenkins job as per Jenkinsfile in the current repo
<img width="858" height="227" alt="image" src="https://github.com/user-attachments/assets/15443c0e-54e7-428d-8bbd-8bc0cd50dbbb" />  
<br /><br /><br />


## Check pods
<img width="606" height="106" alt="image" src="https://github.com/user-attachments/assets/b6b1e0d4-21c7-4a37-b8b8-cbbdc718c306" />  
<br /><br /><br />


## Check image on ECR
<img width="1701" height="925" alt="image" src="https://github.com/user-attachments/assets/053621f7-e808-4493-8f18-99b1b05a9273" />
<br /><br /><br />

## Version increment
- the pipeline also increments version using the following stages
```
stage('increment version') {
    steps {
        script {
            echo 'incrementing app version...'
            sh 'mvn build-helper:parse-version versions:set \
                -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                versions:commit'
            def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
            def version = matcher[0][1]
            env.IMAGE_NAME = "$version-$BUILD_NUMBER"
        }
    }
}

stage('commit version update'){
  steps {
      script {
          withCredentials([usernamePassword(credentialsId: 'gitlab-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]){
              sh "git remote set-url origin https://${USER}:${PASS}@github.com/hiteshparashar4/Capstone-Project-1.git"
              sh 'git add .'
              sh 'git commit -m "ci: version bump"'
              sh 'git push origin HEAD:jenkins-jobs'
          }
      }
  }
}      
```




