node {
    stage('Build') {
        git 'https://github.com/leon-schi/spring-petclinic-microservices-swagger'
        env.GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    }
    
    stage('Testing') {
        def server = 'http://172.20.8.218:8761/'
        parallel (
            "Anchore" : {
                // put all images to analyze in one file
                sh 'echo debian:latest > anchore_images'
                sh 'echo ubuntu:latest >> anchore_images' 
                anchore name: 'anchore_images'
            },
            "ZAP vets-service" : {
                // start the vets-service
                dir("spring-petclinic-vets-service") {
                    sh 'sudo nohup mvn spring-boot:run &'
                }
                
                zap gitCommitId: env.GIT_COMMIT, applicationName: 'spring-petclinic-vets-service', server: server
            },
            "ZAP customers-service" : {
                // start the customers-service
                dir("spring-petclinic-customers-service") {
                    sh 'sudo nohup mvn spring-boot:run &'
                }
                
                zap gitCommitId: env.GIT_COMMIT, applicationName: 'spring-petclinic-customers-service', server: server
            },
            "ZAP api-gateway" : {
                // start the customers-service
                dir("spring-petclinic-api-gateway") {
                    sh 'sudo nohup mvn spring-boot:run &'
                }
                
                zap gitCommitId: env.GIT_COMMIT, applicationName: 'spring-petclinic-api-gateway', server: server
            },
            "ZAP visits-service" : {
                // start the customers-service
                dir("spring-petclinic-visits-service") {
                    sh 'sudo nohup mvn spring-boot:run &'
                }
                
                zap gitCommitId: env.GIT_COMMIT, applicationName: 'spring-petclinic-visits-service', server: server
            }
        )
    }
}