import hudson.model.*
import hudson.FilePath
import hudson.EnvVars
import groovy.json.JsonSlurperClassic
import groovy.json.JsonBuilder
import groovy.json.JsonOutput
import java.net.URL
import groovy.transform.Field
import java.text.SimpleDateFormat

node{
    wrap([$class: 'TimestamperBuildWrapper']) {
    def wsDir = "./${env.BRANCH_NAME}"
    ws (wsDir) {
        dast()
        posting_to_elk()
    }
  }   
}  

def dast(){
    stage('DAST'){
        docker.image('owasp/zap2docker-weekly').inside{
            sh '''
                zap-cli start --start-options '-config api.disablekey=true'
                zap-cli status -t 120
                zap-cli open-url http://${APP_URL}
                zap-cli spider http://${APP_URL}
                zap-cli active-scan --recursive http://${APP_URL}
                zap-cli alerts -l Informational --exit-code false
                zap-cli alerts -f json -l Informational --exit-code false >> output.json 
            '''
      }
    }
}

def posting_to_elk(){
    stage('Posting to ELK'){
        def dateFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSSZ")
        def date = new Date()
        def timestamp = dateFormat.format(date)
        docker.image('rsmartins78/alpine-jq-bash').inside{
             sh "cat output.json | jq -c '.[] + {\"insert_date\": \"${timestamp}\",\"app_name\": \"${APP_NAME}\"} | {\"index\": {\"_index\": \"bookmarks\", \"_type\": \"bookmark\"}}, .' | curl -H 'Content-Type: application/json' -XPOST ${ELASTIC_ADDRESS}/_bulk --data-binary @-"
      }
   }
}
