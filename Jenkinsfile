
node {

    def imageName = "YOUR_DOCKERHUB_USERNAME/oltrs:latest"

    properties([
        pipelineTriggers([
            githubPush()
        ])
    ])

    try {

        stage('Checkout') {

            git branch: 'main',
                url: 'https://github.com/archanareddyse/OLTRS.git'

        }

        stage('Build') {

            sh 'mvn clean package -DskipTests'

        }

        stage('Test') {

            sh 'mvn test'

        }

        stage('Docker Build') {

            sh "docker build -t ${imageName} ."

        }

        stage('Docker Push') {

            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {

                sh '''
                    echo "$DOCKER_PASS" |
                    docker login -u "$DOCKER_USER" --password-stdin
                '''

                sh "docker push ${imageName}"

                sh 'docker logout'
            }
        }

    } catch (Exception e) {

        currentBuild.result = 'FAILURE'

        echo "Pipeline failed: ${e}"

        throw e
    }
}
