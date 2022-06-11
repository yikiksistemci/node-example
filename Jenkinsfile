node {
    def app

    stage('Show Parameters') {
        script {
            echo "Registery: ${params.REGISTERY}"
            currentBuild.displayName = "${BUILD_NUMBER}.${params.REGISTERY}"
        }  
    }

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }
    
    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        if (params.REGISTERY == "DockerHub"){
            app = docker.build("yunusyasar/hellonode")
        }
        if (params.REGISTERY == "GitLabRegistery") {
            app = docker.build("registry.gitlab.com/bootcamp231/hellonode")
        }
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image DockerHub') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */

        echo 'Your Registery: ${params.REGISTERY}'
        if (params.REGISTERY == "DockerHub"){
            docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
                app.push("${env.BUILD_TAG}")
            }
        }

        if (params.REGISTERY == "GitLabRegistery"){
            docker.withRegistry('https://registry.gitlab.com', 'gitlab-registery') {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
                app.push("${env.BUILD_TAG}")
            }
        }
    }
}
