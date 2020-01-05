def createRepoTemplate(String repo_name_loc){
def url = "https://api.github.com/repos/lakshmanavinod/npm_cicd_template/generate"
def conn = new URL(url).openConnection();
def body = """{
  "owner": "lakshmanavinod",
  "name": ${repo_name_loc},
  "description": "This is your first repository",
  "private": false
}"""
conn.setRequestMethod("POST")
conn.setDoOutput(true)
//Basic Authentication
withCredentials([usernamePassword(credentialsId: 'GIT-ACCESS', passwordVariable: 'USER_PASSWD', usernameVariable: 'USER_NAME')]) {
    // some block
String user_details = "${USER_NAME}"+":"+"${USER_PASSWD}"
}
String auth = user_details.bytes.encodeBase64().toString()
// End of Auth
conn.setRequestProperty("Authorization","Basic "+ auth);
conn.setRequestProperty("Content-Type", "application/json");
conn.setRequestProperty("Accept", "application/vnd.github.baptiste-preview+json");
conn.getOutputStream().write(body.getBytes("UTF-8"));
def postRC = conn.getResponseCode();
println(postRC);
if(postRC.equals(200)) {
    println(conn.getInputStream().getText());
}
}

def createMultiBranchPipeline(String repo_name_loc){
  multibranchPipelineJob("${repo_name_loc}") {
    branchSources {
        git {
           // id('123456789') IMPORTANT: use a constant and unique identifier
            remote("https://github.com/lakshmanavinod/${repo_name_loc}.git")
            credentialsId('GIT-ACCESS')
        }
    }
    orphanedItemStrategy {
        discardOldItems {
            numToKeep(20)
        }
    }
}

}
def registerWebhook(String repo_name_loc){
def url = "https://api.github.com/repos/lakshmanavinod/${repo_name_loc}/hooks"
def conn = new URL(url).openConnection();
def body = """{
  "config": {
    "url": "http://cicd.jenkins.techforce.ai/github-webhook/",
    "content_type": "json",
    "insecure_ssl": "0"
  }
}"""
conn.setRequestMethod("POST")
conn.setDoOutput(true)
//Basic Authentication
withCredentials([usernamePassword(credentialsId: 'GIT-ACCESS', passwordVariable: 'USER_PASSWD', usernameVariable: 'USER_NAME')]) {
    // some block
String user_details = "${USER_NAME}"+":"+"${USER_PASSWD}"
}
String auth = user_details.bytes.encodeBase64().toString()
// End of Auth
conn.setRequestProperty("Authorization","Basic "+ auth);
conn.setRequestProperty("Content-Type", "application/json");
conn.setRequestProperty("Accept", "application/vnd.github.baptiste-preview+json");
conn.getOutputStream().write(body.getBytes("UTF-8"));
def postRC = conn.getResponseCode();
println(postRC);
if(postRC.equals(200)) {
    println(conn.getInputStream().getText());
}
}

pipeline {
  agent any
  stages {
    stage('Create Repo') {
      steps {
        // Capture the values w.r.t application
        createRepoTemplate("${repo_name}")
      }
    }
    stage('Create Pipeline') {
      steps {
        createMultiBranchPipeline("${repo_name}")
      }
    }
    stage('Register Webhook') {
      steps {
       registerWebhook("${repo_name}") 
      }
    }
  }
}
