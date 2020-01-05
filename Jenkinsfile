def createRepoTemplate(String repo_name){
def url = "https://api.github.com/repos/digitamizers/npm_cicd_template/generate"
def conn = new URL(url).openConnection();
def body = """{
  "owner": "digitamizers",
  "name": ${repo_name},
  "description": "This is your first repository",
  "private": false
}"""
conn.setRequestMethod("POST")
conn.setDoOutput(true)
//Basic Authentication
withCredentials([usernamePassword(credentialsId: 'GIT-ACCESS', passwordVariable: 'USER_PASSWD', usernameVariable: 'USER_NAME')]) {
    // some block
String user_details = ${USER_NAME}+':'+${USER_PASSWD}
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

def createMultiBranchPipeline{
multibranchPipelineJob(String repo_name) {
    branchSources {
        git {
           // id('123456789') IMPORTANT: use a constant and unique identifier
            remote("https://github.com/digitamizers/${repo_name}.git")
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
def registerWebhook(String repo_name){
def url = "https://api.github.com/repos/digitamizers/${repo_name}/hooks"
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
String user_details = ${USER_NAME}+':'+${USER_PASSWD}
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
  environment {
    DOCKER_SERVER = credentials('DOCKER_SERVER')
    DOCKER_USERNAME = credentials('DOCKER_USERNAME')
    DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
  }
}
