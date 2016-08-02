node {

    stage 'Checkout'
    
    if (! env.BRANCH_NAME) {
        env.BRANCH_NAME = 'develop'
        // checkout source
        git url: "https://github.com/BCCVL/docs.git/", branch: env.BRANCH_NAME
    } else {
        checkout scm
    }

    stage 'Build'

    docker.image('python:3.5.2-alpine').inside("-u root") {
        sh("apk --update add zlib zlib-dev libjpeg-turbo-dev libpng-dev gcc libc-dev make")
        sh("pip install -r requirements.txt")
        sh("make html")
    }

    // Build image
    stage 'Build Image'

    def version = getPythonVersion('conf.py')
    def imagename = "hub.bccvl.org.au/bccvl/docs:${version}-${env.BUILD_NUMBER}"

    def image = docker.build(imagename)

    if (env.BRANCH_NAME == 'master') {
        // only deploy master branch
        stage 'Push Image'

        image.push()

        stage 'Deploy'

        deploy("Docs", env.BRANCH_NAME, imagename);
    }
}
