pipeline {
    agent any
    tools {
        nodejs "node"
    }
    stages {
        stage ("Version Increment") {
            steps {
                script {
                    dir("app") {
                        sh "npm version patch"
                        def packageJson = readJSON file: 'package.json'
                        def versionNumber = packageJson.version
                        env.IMAGE_NAME = "$versionNumber-$BUILD_NUMBER"
                    }
                }
            }
        }
        stage ("Testing") {
            steps {
                script {
                    dir("app") {
                        sh "npm install"
                        sh "npm run test"
                    }
                }
            }
        }
        stage("Build and push image to docker repo") {
            steps {
                script {
                    echo "building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'my-docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                        sh "docker build -t jkiks/demo-app:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push jkiks/demo-app:${IMAGE_NAME}"
                    }
                }
            }
        }
        stage("Commit App Version to Repo") {
            steps {
                script {
                    echo "committing updated version to git..."
                    sh 'git config user.email "jenkins-exercise@example.com"'
                    sh 'git config user.name "jenkins-exercise"'
                    sh 'git status'
                    sh 'git branch'
                    sh 'git config --list'
                    sh 'git remote set-url origin git@github.com/J-kiks/jenkins-exercise.git'
                    sh 'git add .'
                    sh 'git commit -m "committing new app version"'
                    sh 'git push origin HEAD:master'
                }
            }
        }
    }
}
