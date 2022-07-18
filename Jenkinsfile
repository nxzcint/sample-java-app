pipeline { 
  
  agent any
  
    stages { 
 
        stage('Build'){ 
            steps { 
                sh "mvn clean" 
 
                sh "mvn compile" 
            } 
        } 
 
        stage('Test'){ 
            steps { 
 
                sh "mvn test" 
            } 
 
            post { 
                always { 
                    junit '**/target/surefire-reports/TEST-*.xml' 
                } 
            } 
        } 
 
        stage('Package'){ 
            steps { 
 
                sh "mvn package" 
            } 
            post { 
                success { 
                    archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false 
                } 
            } 
        } 
 
    } 
 
}
