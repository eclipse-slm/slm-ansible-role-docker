def scenarios = [
    "ubuntu2204": [
        "install-linux-os",
        "install-tcp-linux-os",
        "uninstall-linux-os"
    ],
    "ubuntu2004": [
        "install-linux-os",
        "install-tcp-linux-os",
        "uninstall-linux-os"
    ],
    "ubuntu1804": [
        "install-linux-os",
        "install-tcp-linux-os",
        "uninstall-linux-os"
    ],
    "centos9": [
            "install-linux-os",
            "install-tcp-linux-os",
            "uninstall-linux-os"
    ],
    "centos8": [
        "install-linux-os",
        "install-tcp-linux-os",
        "uninstall-linux-os"
    ],
    "centos7": [
        "install-linux-os",
        "install-tcp-linux-os",
        "uninstall-linux-os"
    ]
//    ,
//    "win10-winrm": [
//        "install-win10",
//        "install-tcp-win10",
//        "uninstall-win10"
//    ],
//    "win10-ssh": [
//        "install-win10",
//        "install-tcp-win10",
//        "uninstall-win10"
//    ]
]

def role = "docker"

parallel_stages = [:]

for (kv in mapToList(scenarios)) {
    def platform = kv[0]
    def scenarioList = kv[1]

    parallel_stages[platform] = {
        docker.image("${MOLECULE_DOCKER_IMAGE}").inside('-u root') {

            stage("Install dependencies") {
                sh "ansible-galaxy install -f -r requirements.yml"
            }

            stage("${platform} - Create") {
                sh "cd ./${role} && molecule create -s install-${platform}-os"
            }

            try {
                for(int i = 0; i < scenarioList.size(); i++) {
                    def scenario = scenarioList[i]


                        stage("${platform} - ${scenario}") {
                            sh "cd ./${role} && molecule -vv test -s ${scenario} -p ${platform} --destroy never"
                        }

                }
            } finally {
                stage("Destroy") {
                    sh "cd ./${role} && molecule destroy -s install-linux-os"
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
            sh "ansible-galaxy install -r requirements.yml"
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