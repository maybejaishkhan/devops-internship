# Version Control

A system that keeps track of changes done to files, allows collaboration, tracks revisions, and reverts to previous versions (when needed). It is basically mandatory for software development, document editing, and collaborative projects.

This is done via "Version Control Systems" and there are two main types:

* **LVCS** (Local VCS): Tracks changes on a single system (Perforce, SVN).
* **DVCS** (Distributed VCS): Each user has a full copy of the repository, enabling offline work and faster operations (Git, Mercurial).

> [Git](git.md) is the default VCS used all over the world. Around 90%+ of all projects use git.

## VCS Hosting Providers

Platforms that host code repositories, manage access control, and offer tools for collaboration, issue tracking, and CI/CD integration. These days all VCS Hosting Providers are Git-based.

* **Remote Repositories**: Store and sync code in the cloud.
* **Access Management**: Control who can read, write, or administer repositories.
* **Issue & Bug Tracking**: Built-in tools to report and manage development tasks.
* **Code Review Workflows**: Pull/merge requests with inline comments and approval flows.
* **CI/CD Integration**: Automate building, testing, and deploying code.

The main VCS Hosting providers are **GitHub**, **GitLab** and **BitBucket**

### **GitHub**

The world’s largest and most popular hosting service for Git repositories. It provides a cloud-based platform where individuals and teams can store, share, and collaborate on Git-based projects. It has been owned by Microsoft since 2018.

* GitHub != Git. It hosts repositories on remote servers.
* Features: pull requests, issues, wikis, discussions, GitHub Pages for static sites, and GitHub Codespaces for cloud development.
  * [GitHub Actions](./github-actions.md) (CI/CD)
* Supports **public open-source** and **private repositories**.

> Massive developer community and huge ecosystem of third-party integrations.

### **GitLab**

An open-core, web-based DevOps platform that provides Git repository hosting and a full suite of integrated software development and deployment tools — all in one application.

* Fully self-hosted or cloud-hosted options.
* Core features: repositories, merge requests, issues, wikis, GitLab Pages for static sites, and a built-in Web IDE.
  * [GitLab](./gitlab-ci.md)
* Strong DevOps and automation tools: Auto DevOps, Container Registry, Dependency Scanning, Feature Flags, and Service Desk.

> Popular with teams that prefer an all-in-one DevOps solution with flexibility for self-hosting.

### **Bitbucket**

A Git repository hosting service by Atlassian, known for tight integration with Jira, Trello, and the Atlassian ecosystem.

* Core features: repositories, pull requests, pipelines (CI/CD), issues (via Jira), wiki, and basic static site hosting.
* Offers cloud hosting, server, and data center deployment for enterprise teams.
* Deep integration with Jira for issue linking, smart commits, and Trello boards.

> Primarily used by teams already using Atlassian tools for project management and tracking.

### Comparison of VCS Hosting Providers

| **Category** | **GitHub** | **GitLab** | **Bitbucket** |
| --- | --- | --- | --- |
| **Code Review & Collaboration**| Pull Requests, Issues, Discussions, Projects, Wiki, Gists / Snippets | Merge Requests, Issues, Discussions, Issue Boards, Wiki, Snippets  | Pull Requests, Issues (Jira integration), — (use Jira/Confluence), Trello/Jira Boards integration, Wiki, Snippets |
| **CI/CD & Automation**| Actions (CI/CD), Dependabot (Dependency Updates)| Pipelines (CI/CD), Auto DevOps, Dependency Scanning, Feature Flags, Service Desk | Pipelines|
| **Package & Container Management** | Packages| Package Registry, Container Registry| Pipelines Artifacts (basic) |
| **Static & Hosting**| Pages (Static Sites)| Pages| Static Sites |
| **Integrated Dev Environment**  | Codespaces (Online IDE)| Web IDE | — (use VS Code / Sourcetree)|
| **Marketplace & Apps** | Marketplace| Marketplace| Atlassian Marketplace |
| **Integrations & Ecosystem** | — | — | Tight Jira Integration, Trello Integration, Smart Commits, Sourcetree (GUI Git client)  |
| **API & CLI** | API, CLI | API, CLI | API — (use Git + REST)|
| **Mobile Access**| Mobile App | Mobile App | Mobile App|
