# Jenkins

An open-source automation server written in Java used to automate parts of the software development process, including building, testing, and deploying code.

## Master-Agent Model

* **Jenkins Master**:

  * Orchestrates jobs
  * Provides the UI and REST API
  * Manages queue and assigns jobs to agents
* **Jenkins Agent (Node)**:

  * Executes jobs
  * Can be physical, virtual, Docker container, or Kubernetes pod

### 2.2 Types of Agents

* **Permanent Agent**: Static and always connected
* **Dumb Slave**: Older term for simple agents
* **Cloud Agents**: Ephemeral (e.g., Kubernetes, Docker)

### 2.3 Communication

* **Protocols**: JNLP (Java Web Start), SSH, WebSockets

## Jenkinsfile

* Written in **Groovy DSL**
* Defines pipeline structure
* Stored in source control (Git)
* Types:

  * Declarative (recommended)
  * Scripted (more flexible, complex)

### 3.2 Jobs / Projects

* **Freestyle Project**: Basic UI-based job
* **Pipeline Job**: Code-defined build pipeline
* **Multibranch Pipeline**: Auto-discovers branches with Jenkinsfiles
* **Matrix Job**: Builds with different configurations

### 3.3 Plugins

* Over 1800 plugins available
* Examples:

  * Git, GitHub, Bitbucket
  * Slack Notifications
  * Docker, Kubernetes
  * SonarQube, Jacoco
  * Blue Ocean (UI)

---

## 🔄 **4. Pipeline Concepts**

### 4.1 Stages & Steps

* **Stage**: Logical grouping (e.g., Build, Test, Deploy)
* **Step**: Single task (e.g., `sh 'npm install'`)

### 4.2 Triggers

* SCM Polling
* Webhooks
* Scheduled (CRON)
* Manual

### 4.3 Parallelism & Agents

* Parallel execution using `parallel`
* Different stages can run on different agents

---

## 🔒 **5. Jenkins Security**

* **Authentication**:

  * Jenkins internal DB
  * LDAP, SSO (SAML, OIDC)
* **Authorization**:

  * Matrix-based
  * Role-based (via plugin)
* **Secrets Management**:

  * Jenkins Credentials Plugin
  * Integration with Vault, AWS Secrets Manager

---

## 🌐 **6. Integration**

* **Source Control**: Git, GitHub, GitLab, Bitbucket
* **Build Tools**: Maven, Gradle, Ant, npm
* **Test Frameworks**: JUnit, NUnit, TestNG
* **Deployment**: Ansible, Terraform, Helm, K8s
* **Notification**: Email, Slack, MS Teams
* **Artifact Repos**: Nexus, Artifactory, S3

---

## 📦 **7. Deployment Options**

* **Standalone WAR**: Run directly using `java -jar jenkins.war`
* **Docker Image**: Official `jenkins/jenkins`
* **Kubernetes**: Helm chart or custom operator
* **Package Managers**:

  * Debian: `apt install jenkins`
  * Red Hat: `yum install jenkins`

---

## 🛠️ **8. Configuration & Administration**

* **Configuration Files**:

  * `/var/lib/jenkins/config.xml`
  * `JENKINS_HOME` for jobs, plugins, builds
* **Backup Strategy**:

  * Backup `JENKINS_HOME`
  * Use plugins like ThinBackup or Job Configuration History
* **Logs**:

  * Default: `/var/log/jenkins/jenkins.log`
  * Pipeline logs shown in the UI per job

---

## 📊 **9. Monitoring & Scaling**

* **Monitoring**:

  * Metrics plugin, Prometheus exporter
  * Integrate with Grafana
* **Scaling**:

  * Master with multiple agents
  * Auto-scaling agents on Kubernetes/Docker

---

## ✅ **10. Best Practices**

* Use **Declarative Pipelines**
* Store pipelines as **code** in version control
* **Isolate credentials** using secure credentials store
* Keep Jenkins and plugins **up to date**
* Use **agents** for scalability and isolation
* Enable **audit logs** and monitor user actions
* Avoid running builds on master
* Implement **code review** for Jenkinsfiles

---

## 📚 **11. Learning & Documentation**

* **Official Docs**: [https://www.jenkins.io/doc/](https://www.jenkins.io/doc/)
* **Book**: *Jenkins: The Definitive Guide*
* **Courses**: Udemy, Coursera, LinkedIn Learning
* **Community**: Jenkins mailing list, Stack Overflow, GitHub

```Jenkinsfile
pipeline {
  agent any  // Runs on any available agent

  environment {
    NODE_ENV = 'production'
    AWS_REGION = 'us-east-1'
  }

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/your-org/your-repo.git', branch: 'main'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
        archiveArtifacts artifacts: 'dist/**', fingerprint: true
      }
    }

    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY')]) {
          sh './deploy.sh'
        }
      }
    }
  }

  post {
    always {
      echo "Cleaning up..."
    }
    success {
      echo "Build and Deploy successful!"
    }
    failure {
      mail to: 'dev-team@example.com',
           subject: "Jenkins Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
           body: "Check console output at ${env.BUILD_URL}"
    }
  }
}

```
