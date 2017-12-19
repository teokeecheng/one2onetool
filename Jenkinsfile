pipeline {
node {
   def awsKeyPath
   def awsUrl
   def awsUser
   def appProcess
   def gitBranchName 
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/teokeecheng/one2onetool.git'
      
      awsKeyPath='/Users/Shared/Jenkins/task2/task2.pem'
      awsUrl='ec2-13-250-125-68.ap-southeast-1.compute.amazonaws.com'
      awsUser='ec2-user'
      appProcess='/home/ec2-user/server/node_modules/forever/bin/monitor index.j[s]'
      echo env.BRANCH_NAME
      echo 'testing1'
   }
  stage('Build') {
      sh 'npm install'
  }
  stage('Test') {
      sh 'npm test'
  }
  stage('Deploy') {
      echo 'Stop application...'
      sh "ssh -i ${awsKeyPath} -t ${awsUser}@${awsUrl} 'cd ~/server; COUNT=\$((`ps -ef | grep \"${appProcess}\" | wc -l`)); echo \$COUNT; if [ \$COUNT -gt 0 ]; then npm stop; fi'"
      echo 'Backup deployment directory...'
      sh "ssh -i ${awsKeyPath} -t ${awsUser}@${awsUrl} 'DATE=`date +%Y%m%d%H%M`;mkdir -p ~/backup/\$DATE;mv ~/server/* ~/backup/\$DATE'"
      echo 'Copy deployment files...'
      sh "scp -i ${awsKeyPath} -r ./* ${awsUser}@${awsUrl}:~/server/"
      echo 'Start application...'
      sh "ssh -i ${awsKeyPath} -t ${awsUser}@${awsUrl} 'cd ~/server; npm rebuild'"
      sh "ssh -i ${awsKeyPath} -t ${awsUser}@${awsUrl} 'cd ~/server; npm start'"
  }
}
}