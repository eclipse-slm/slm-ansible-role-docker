def default_scenarios = [
    "install",
    "uninstall"
]

def scenarios = [
    "ubuntu2404":   default_scenarios,
    "ubuntu2204":   default_scenarios,
    "ubuntu2004":   default_scenarios,
    "ubuntu1804":   default_scenarios,
    "centos10":     default_scenarios,
    "centos9":      default_scenarios,
    "centos8":      default_scenarios,
    "centos7":      default_scenarios,
    "debian13":     default_scenarios,
    "debian12":     default_scenarios,
    "debian11":     default_scenarios
]

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
                        sh "molecule ${verbose} test -s ${scenario}-${platform} --destroy never --report"
                    }
                }
            } finally {                
                stage("Destroy") {
                    sh "molecule destroy -s ${scenarioList[0]}-${platform} --report"
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