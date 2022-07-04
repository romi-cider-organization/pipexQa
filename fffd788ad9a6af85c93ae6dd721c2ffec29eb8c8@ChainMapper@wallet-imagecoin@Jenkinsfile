def withPod(body) {
  podTemplate(cloud: 'chainmapper', containers: [
      containerTemplate(name: 'kaniko', image: 'gcr.io/kaniko-project/executor:debug', command: '/busybox/cat', ttyEnabled: true)
    ],
    volumes: [
      secretVolume(secretName: 'netwatwezoeken', mountPath: '/cred')
    ]
 ) { body() }
}

withPod {
    node(POD_LABEL) {
        def tag = "${env.BRANCH_NAME.replaceAll('/', '-')}-${env.BUILD_NUMBER}"
        if (env.TAG_NAME != null) {
            tag = env.TAG_NAME
        } else if (env.BRANCH_NAME == 'master') {
            tag = "latest"
        }
        def registry = "packages.netwatwezoeken.nl/chainmapper"
        def appname = "wallet-imagecoin"
        def service = "${registry}/${appname}:${tag}"
        checkout scm        
        container('kaniko') {
            stage('Build image') {
                sh("cp /cred/.dockerconfigjson /kaniko/.docker/config.json")
                sh("executor --context=`pwd` --dockerfile=`pwd`/Dockerfile --destination=${service} --single-snapshot")
            }    
        }        
    }
}