node {

    stage 'Checkout'

    scm checkout

    docker.withRegistry('https://hub.bccvl.org.au', 'hub.bccvl.org.au') {
        docker.withServer('unix:///var/run/docker.sock') {

            stage 'Build'

            docker.image('python:3.5.-apline').inside("-u root") {
                sh("pip install -r requirements.txt")
                sh("make html")
                // dir("_build/html") {
                //     stash("docs.bccvl.org.au")
                // }
                // Build should now be available in {workdir}/_build/html
            }

            // Build image
            stage 'Build Image'

            def imagename = "hub.bccvl.org.au/bccvl/docs"

            def image = docker.build(imagename)

            stage 'Push Image'

            image.push()

            stage 'Deploy'

            deploy("Docs", env.BRANCH_NAME, imagename);
        }
    }
}
