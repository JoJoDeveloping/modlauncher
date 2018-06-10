pipeline {
    agent {
        docker {
            image 'gradle:latest'
            args '-v gradlecache:/home/gradle/.gradle'
        }
    }
    environment {
        GRADLE_OPTS='--no-daemon --info'
    }
    stages {
        stage('fetch') {
            steps {
                git(url: 'https://github.com/cpw/modlauncher.git', changelog: true)
            }
        }
        stage('buildandtest') {
            steps {
                sh './gradlew --refresh-dependencies --continue build test'
            }
        }
        stage('publish') {
            environment {
                FORGE_MAVEN = credentials('forge-maven-cpw-user')
            }
            steps {
                sh './gradlew publish -PforgeMavenUser=${FORGE_MAVEN_USR} -PforgeMavenPassword=${FORGE_MAVEN_PSW}'
                sh 'curl --user ${FORGE_MAVEN} http://files.minecraftforge.net/maven/manage/promote/latest/cpw.mods.modlauncher/${BUILD_NUMBER}'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/test-results/**/*.xml'
            jacoco sourcePattern: '**/src/*/java'
        }
    }
}