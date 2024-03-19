pipeline {
  agent any

  environment {
    NAME = "solar-system13"
    VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
    IMAGE_REPO = "chevvusandeep"
    ARGOCD_TOKEN = credentials('argocd-token')
    GITEA_TOKEN = credentials('github-token')
  }
  
  stages {
    stage('Unit Tests') {
      steps {
        echo 'Implement unit tests if applicable.'
        echo 'This stage is a sample placeholder'
      }
    }

    stage('Build Image') {
      steps {
            sh "docker build -t ${NAME} ."
            sh "docker tag ${NAME}:latest ${IMAGE_REPO}/${NAME}:${VERSION}"
        }
      }

    stage('Push Image') {
      steps {
        withDockerRegistry([credentialsId: "docker-PAT", url: ""]) {
          sh 'docker push ${IMAGE_REPO}/${NAME}:${VERSION}'
        }
      }
    }

    stage('Clone/Pull Repo') {
      steps {
        script {
          if (fileExists('Wezva-Argocd')) {

            echo 'Cloned repo already exists - Pulling latest changes'

            dir("Wezva-Argocd") {
              sh 'git pull'
            }

          } else {
            echo 'Repo does not exists - Cloning the repo'
            sh 'git clone -b main https://github.com/chevvusandeep12/Wezva-Argocd'
          }
        }
      }
    }
    
    stage('Update Manifest') {
      steps {
        dir("Gitops/jenkins-demo") {
          sh 'sed -i "s#chevvusandeep.*#${IMAGE_REPO}/${NAME}:${VERSION}#g" deployment.yaml'
          sh 'cat deployment.yaml'
        }
      }
    }

    stage('Commit & Push') {
      steps {
        dir("Gitops/jenkins-demo") {
          sh "git config --global user.email 'chevvusandeep12@gmail.com'"
          sh 'git remote set-url origin http://$GITHUB_TOKEN@github.com/chevvusandeep12/Wezva-Argocd'
          sh 'git checkout main'
          sh 'git add -A'
          sh 'git commit -am "Updated image version for Build - $VERSION"'
          sh 'git push origin main'
        }
      }
    }
  }
}
