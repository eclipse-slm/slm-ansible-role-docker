def scenarios = [
    "ubuntu2404": [
        "install-linux",
        //"install-tcp-linux-os",
        "uninstall-linux"
    ],
//    "ubuntu2204": [
//        "install-linux",
//        //"install-tcp-linux-os",
//        "uninstall-linux"
//    ],
//    "ubuntu2004": [
//        "install-linux",
//        //"install-tcp-linux-os",
//        "uninstall-linux"
//    ],
//    "ubuntu1804": [
//        "install-linux",
//        //"install-tcp-linux-os",
//        "uninstall-linux"
//    ],
//    "centos10": [
//            "install-linux",
//            //"install-tcp-linux-os",
//            "uninstall-linux"
//    ],
//    "centos9": [
//            "install-linux",
//            //"install-tcp-linux-os",
//            "uninstall-linux"
//    ],
//    "centos8": [
//        "install-linux",
//        //"install-tcp-linux-os",
//        "uninstall-linux"
//    ],
//    "centos7": [
//        "install-linux",
//        //"install-tcp-linux-os",
//        "uninstall-linux"
//    ],
//     "debian13": [
//         "install-linux",
//         //"install-tcp-linux-os",
//         "uninstall-linux"
//     ],
//     "debian12": [
//         "install-linux",
//         //"install-tcp-linux-os",
//         "uninstall-linux"
//     ],
//     "debian11": [
//         "install-linux",
//         //"install-tcp-linux-os",
//         "uninstall-linux"
//     ]
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
def verbose = "-vv"

parallel_stages = [:]

for (kv in mapToList(scenarios)) {
    def platform = kv[0]
    def scenarioList = kv[1]

    parallel_stages[platform] = {
        docker
          .image("${MOLECULE_DOCKER_IMAGE}")
          .inside("--name ${JOB_NAME}_${platform} -e OS_AUTH_URL=${OS_AUTH_URL} -e OS_USERNAME=${OS_APPLICATION_CREDENTIAL_ID} -e OS_PASSWORD=${OS_APPLICATION_CREDENTIAL_SECRET} -u root") {

            stage("${platform} - Create") {
                sh "ansible --version"
                sh "molecule --version"
                sh "cd ./${role} && molecule ${verbose} create -s install-${platform} --report"
            }

            try {
                for(int i = 0; i < scenarioList.size(); i++) {
                    def scenario = scenarioList[i]


                        stage("${platform} - ${scenario}") {
                            sh "cd ./${role} && molecule ${verbose} test -s ${scenario} -p ${platform} --destroy never --report"
                        }

                }
            } finally {
                stage("Destroy") {
                    sh "cd ./${role} && molecule destroy -s install-linux --report"
                }
            }
        }
    }
}

node {
    checkout https://github.com/eclipse-slm/slm-ansible-role-docker
//    checkout scm

    withCredentials([usernamePassword(
            credentialsId: 'openstack-credentials',
            usernameVariable: 'OS_APPLICATION_CREDENTIAL_ID',
            passwordVariable: 'OS_APPLICATION_CREDENTIAL_SECRET'
    )]) {
        stage("Pull molecule image") {
//            sh "ansible-galaxy install -r requirements.yml"
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