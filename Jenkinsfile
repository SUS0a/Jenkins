node {
    checkout scm
    echo "current branch: $BRANCH_NAME"
    def remote = [:]
     try {
    if (BRANCH_NAME.startsWith("master")) {
         remote.name = 'prod'
         remote.host = '47.107.174.223'
         remote.user = 'root'
         remote.password = 'Yht#369'
         remote.allowAnyHosts = true
         stage('Pull Code') {
                              sshCommand remote: remote, command: "cd /opt/hrg/new_project/sisv/;git pull"
         }
         stage('Build') {
             timeout(time: 30, unit: 'MINUTES') {
                     sshCommand remote: remote, command: "cd /opt/hrg/new_project/sisv/;source /etc/profile;mvn clean package docker:build -Dmaven.test.skip=true"
             }
         }
       }else {
         remote.name = 'dev'
         remote.host = '120.77.93.46'
         remote.user = 'root'
         remote.password = 'Yht#369'
         remote.allowAnyHosts = true
         stage('Pull Code') {
                     sshCommand remote: remote, command: "cd /opt/new_project/sisv/;git pull"
         }
         stage('Build') {
            timeout(time: 30, unit: 'MINUTES') {
                    sshCommand remote: remote, command: "cd /opt/new_project/sisv/;source /etc/profile;mvn clean package docker:build -Dmaven.test.skip=true"
            }
         }
       }
         stage('Run images') {
             sshCommand remote: remote, command: "cd ~;docker-compose down;docker-compose up -d"
         }
         sshCommand remote: remote, command: "cd ~;docker rmi -f \$(docker images -f \"dangling=true\" -q)"
         echo 'This will run only if successful'
    } catch (e) {
         echo 'This will run only if failed'

        throw e
    } finally {
        def currentResult = currentBuild.result ?: 'SUCCESS'
        if (currentResult == 'UNSTABLE') {
            echo 'This will run only if the run was marked as unstable'
        }

        def previousResult = currentBuild.previousBuild?.result
        if (previousResult != null && previousResult != currentResult) {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }

        echo 'This will always run'
    }
}