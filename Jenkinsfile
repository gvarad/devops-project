pipeline {
    agent any
  environment {
        IMAGE_NAME = "varadg10/flask-devops"
    }
    stages {
        stage('Pull Code') {
            steps {
               git branch: 'main',
                    url: 'https://github.com/varadgaikwad/devops-project.git'
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./',
                                odcInstallation: 'dependency-check'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=flask-app \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://13.63.49.237:9000 \
                    -Dsonar.token=squ_20b76e6516fbecd03a83d3fc7854e7da5b7ac3a7
                    '''
                }
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy image $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub']) {
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }
    }
}
