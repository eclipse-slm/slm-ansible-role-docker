def scenarios = [
    "ubuntu2404": [
        "install",
        "uninstall"
    ],
   "ubuntu2204": [
       "install",
       "uninstall"
   ],
   "ubuntu2004": [
       "install",
       "uninstall"
   ],
   "ubuntu1804": [
       "install",
       "uninstall"
   ],
   "centos10": [
        "install",
        "uninstall"
   ],
   "centos9": [
        "install",
        "uninstall"
   ],
   "centos8": [
       "install",
       "uninstall"
   ],
   "centos7": [
       "install",
       "uninstall"
   ],
     "debian13": [
         "install",
         "uninstall"
     ],
     "debian12": [
         "install",
         "uninstall"
     ],
     "debian11": [
         "install",
         "uninstall"
     ]
]

def role = "docker"
def verbose = "-vv"

parallel_stages = [:]

for (kv in mapToList(scenarios)) {
    def platform = kv[0]
    def scenarioList = kv[1]

    parallel_stages[platform] = {
        docker
          .image("${MOLECULE_DOCKER_IMAGE}")
          .inside("--name ${JOB_NAME}_${platform} -e OS_AUTH_URL=${OS_AUTH_URL} -e OS_USERNAME=${OS_APPLICATION_CREDENTIAL_ID} -e OS_PASSWORD=${OS_APPLICATION_CREDENTIAL_SECRET} -u root") {

            try {
                for(int i = 0; i < scenarioList.size(); i++) {
                    def scenario = scenarioList[i]

                    stage("${platform} - ${scenario}") {
                        sh "cd ./${role} && molecule ${verbose} test -s ${scenario}-${platform} --destroy never --report"
                    }
                }
            } finally {                
                stage("Destroy") {
                    sh "cd ./${role} && molecule destroy -s ${scenarioList[0]}-${platform} --report"
                }
            }
        }
    }
}

node {
   checkout scm

    withCredentials([usernamePassword(
            credentialsId: 'openstack-credentials',
            usernameVariable: 'OS_APPLICATION_CREDENTIAL_ID',
            passwordVariable: 'OS_APPLICATION_CREDENTIAL_SECRET'
    )]) {
        stage("Pull molecule image") {
            sh "docker pull ${MOLECULE_DOCKER_IMAGE}"
        }

        parallel(parallel_stages)
    }
}

@NonCPS
List<List<?>> mapToList(Map map) {
    return map.collect { it ->
        [it.key, it.value]
    }
}