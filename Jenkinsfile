   stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh('cd k8s-wordsmith-demo')
        sh('docker-compose build')
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://529505258873.dkr.ecr.us-east-1.amazonaws.com', '311b4a0a-dde2-49f5-a5f1-3d250858536f') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    stage('Smart Check') {
        sh './scan.sh'
    }
    stage('Deploy') {
        sh('kubectl run devchatdemo --image=529505258873.dkr.ecr.us-east-1.amazonaws.com/devchatdemo:latest --port 8080')
        sh('sleep 5')
        sh('kubectl expose deployment devchatdemo --type=LoadBalancer --port 80 --target-port 8080')
        sh('sleep 20')
        sh('kubectl get svc --namespace default devchatdemo --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}"')
    }
