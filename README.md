# Jenkinsfile
What is a ***Jenkinsfile ?***  
- Automate pipeline setup with Jenkinsfile
- Jenkinsfile defines stages in CI/CD pipeline
- Jenkinsfile is a text file with pipeline DSL syntax
- Similar to groovy
  - Scripted
  - Declarative

### Jenkinsfile flow :
```mermaid
graph LR
A(fetch code <br> from Git) -->B(mvn <br> build) -->C(mvn <br> unit test) -->D(mvn <br> checkstyle <br> code analysis)
-->E(sonarqube <br> code analysis) -->F(sonarqube <br> quality gate <br> check) -->G(upload artifact <br> to nexus)
-->H(send notification <br> to <br> slack channel)
   
```
### Steps :
#### <ins> *Note* </ins>  : Keep updating the Jenkinsfile while doing the following steps.
- Create three servers for: ([Click here for installation and provisioning of the servers](https://github.com/yogeshgunasekaran/Automated-Provisioning-Project-2))
    - **Jenkins**
    - **SonarQube**
    - **Nexus**
- In Jenkins server - **Manage Plugins:**
  - add **SonarQube Scanner** plugin
  - add **Nexus Artifact Uploader** plugin
  - add **Pipeline Maven Integration** plugin
  - add **Pipeline Utility Steps** plugin
  - add **Build Timestamp** plugin
  - add **Slack Notification** plugin
- In Jenkins server - **Global Tool Configuration:**
  - Add **SonarQube Scanner**
    - configure sonarqube scanner with name as **sonar4.7**
- In Jenkins server - **Configure System:** <br>
  - configure sonarqube server details and integrate it with jenkins,
    - checkbox the **Environmental variables**
    - add name as **sonar**
    - add server url **http ://sonarqube-ip:9000**
    - Go to **sonarqube server &rarr; administrator &rarr; Security &rarr; Generate tokens** 
    - Add credentials as **secret text** with sonarqube token and ID & Description as **MySonarToken**
  - configure Build Timestamp
    - checkmark **Enable Build Timestamp**
    - choose the required **timezone** and its **pattern** 
  - configure the Slack details and integrate it with jenkins as,
    -  Add Slack **Workspace** name
    -  In Slack, Add **Jenkins CI** app and select the channel to receive the notifications. Copy the token and save the settings.
    -  Add credentials as **secret text** with **slack token** and ID & Description as **SlackToken**
    -  Add default **channel name** of slack 
    -  Test Connection
- In sonarqube server create **Quality Gate** with required **conditions**  
- In sonarqube server click our **project &rarr; project settings &rarr; select the Quality Gate** that has been created
- In sonarqube server click our **project &rarr; project settings &rarr; Webhooks &rarr; Create**
    - Give a name **jenkins-ci-webhook**
    - URL **http ://jenkins-ip-here:8080/sonarqube-webhook**
- Login to nexus server and **Create repository &rarr; maven2(hosted)** with any name
- In Jenkins server - **Manage Credentials:**
  - Add credentials as **username and password**
  - Give the nexus server username and password and ID & Description as **nexus-login**
- In Jenkins create a new **job** as **Pipeline** and paste the **Jenkinsfile script** in the script section with updated details

