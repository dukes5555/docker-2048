pipeline {

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git url:'https://github.com/dukes5555/2048-game.git', branch:'master'
      }
    }

      stage("Build image") {
            steps {
                script {
                    myapp = docker.build("dukes5555/2048-game:${env.BUILD_ID}")
                }
            }
        }

      stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }


      stage('Deploy k8s') {
        steps {
          withKubeConfig([credentialsId: 'k8s_token_12420', serverUrl: 'https://05113FE4378E0380F5942EF679308835.gr7.us-east-1.eks.amazonaws.com']) {
            script {
                    sh(script: "sed 's@{{build_version}}@${env.BUILD_ID}@;' 2048_game.yaml >> 2048_game_final_build.yaml")
                    sh(script: "cat 2048_game_final_build.yaml")
                    sh(script: "kubectl apply -f 2048_game_final_build.yaml -n devteam2")
                }
          }
        }
      }
  }

}
