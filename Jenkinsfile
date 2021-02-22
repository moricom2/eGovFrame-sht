node {
    properties([
        parameters([
            string(defaultValue: 'https://api.sandbox.x8i5.p1.openshiftapps.com:6443', description: 'OpenShift API Server URL을 입력해 주세요.', name: 'PARAM_OCP_APISERVER', trim: true),
            string(defaultValue: 'moricom', description: 'OpenShift 계정 ID를 입력해 주세요.', name: 'PARAM_OCP_USERNAME', trim: true),
            password(defaultValue: '', description: 'OpenShift 계정 비밀번호를 입력해 주세요.', name: 'PARAM_OCP_PASSWORD', trim: true),
            string(defaultValue: 'moricom-stage', description: '대상 Project ID를 입력해 주세요.', name: 'PARAM_OCP_PROJECT', trim: true),
            string(defaultValue: 'eap73-openjdk11-basic-s2i', description: '템플릿 명을 입력해 주세요.', name: 'PARAM_OCP_TEMPLATE_NAME', trim: true),
            string(defaultValue: 'sht', description: 'The name for the application', name: 'APPLICATION_NAME', trim: true),
            string(defaultValue: 'https://github.com/moricom2/eGovFrame-sht.git', description: 'Git source URI for application', name: 'SOURCE_REPOSITORY_URL', trim: true),
            string(defaultValue: '', description: 'Git branch/tag reference', name: 'SOURCE_REPOSITORY_REF', trim: true),
            string(defaultValue: '', description: 'Path within Git project to build; empty for root project directory.', name: 'CONTEXT_DIR', trim: true)])])

    withEnv(["PATH+OC=${tool 'oc4.6'}"]){
        stage('oc login'){
			wrap([$class: 'MaskPasswordsBuildWrapper', varPasswordPairs: [[var: 'PARAM_OCP_PASSWORD', password: PARAM_OCP_PASSWORD]]]){
				sh "oc login --server=${PARAM_OCP_APISERVER} --username=${PARAM_OCP_USERNAME} --password=${PARAM_OCP_PASSWORD}"
			}
        }
        stage('oc project'){
			sh "oc project ${PARAM_OCP_PROJECT}"
        }
        stage('oc create'){
            sh "oc process ${PARAM_OCP_TEMPLATE_NAME} -l app=${APPLICATION_NAME},application=${APPLICATION_NAME} APPLICATION_NAME=${APPLICATION_NAME} SOURCE_REPOSITORY_URL=${SOURCE_REPOSITORY_URL} SOURCE_REPOSITORY_REF=${SOURCE_REPOSITORY_REF} CONTEXT_DIR=${CONTEXT_DIR} -o yaml | oc create -f -"                
        }
        stage('oc logs bc (s2i-build)'){
            sh "oc logs -f bc/${APPLICATION_NAME}-build-artifacts"
        }
        stage('oc logs bc (docker-build)'){
            sh "sleep 120 && oc logs -f bc/${APPLICATION_NAME}"
        }
        stage('oc logs dc'){
            sh "oc logs -f dc/${APPLICATION_NAME}"
        }
    }
}