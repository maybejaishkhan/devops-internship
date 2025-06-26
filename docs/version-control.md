# VCS Hosting Providers
There are 3 main ones.

1. [**GitHub**](#github)
2. [**GitLab**](#gitlab)
3. [**Bitbucket**](#bitbucket)
4. [Comparison of VCS Hosting Providers](#comparison-of-vcs-hosting-providers)

---

## **GitHub**

The world’s largest and most popular hosting service for Git repositories. It provides a cloud-based platform where individuals and teams can store, share, and collaborate on Git-based projects. It has been owned by Microsoft since 2018.

* GitHub != Git. It hosts repositories on remote servers.
* Adds collaboration features: pull requests, issues, wikis, discussions, CI/CD (Actions), Pages for static sites, and Codespaces for cloud development.
* Supports **public open-source** and **private repositories**.

> Massive developer community and huge ecosystem of third-party integrations.

## **GitLab**

An open-core, web-based DevOps platform that provides Git repository hosting and a full suite of integrated software development and deployment tools — all in one application.

* Fully self-hosted or cloud-hosted options.
* Core features: repositories, merge requests, issues, wikis, pipelines (CI/CD), Pages for static sites, and a built-in Web IDE.
* Strong DevOps and automation tools: Auto DevOps, Container Registry, Dependency Scanning, Feature Flags, and Service Desk.

> Popular with teams that prefer an all-in-one DevOps solution with flexibility for self-hosting.

## **Bitbucket**

A Git repository hosting service by Atlassian, known for tight integration with Jira, Trello, and the Atlassian ecosystem.

* Core features: repositories, pull requests, pipelines (CI/CD), issues (via Jira), wiki, and basic static site hosting.
* Offers cloud hosting, server, and data center deployment for enterprise teams.
* Deep integration with Jira for issue linking, smart commits, and Trello boards.

> Primarily used by teams already using Atlassian tools for project management and tracking.

## Comparison of VCS Hosting Providers

| **Category**                       | **GitHub**                                                                                     | **GitLab**                                                                               | **Bitbucket**                                                                                                               |
| ---------------------------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Source Control**                 | Repositories                                                                                   | Repositories                                                                             | Repositories                                                                                                                |
| **Code Review & Collaboration**    | Pull Requests<br>Issues<br>Discussions<br>Kanban Boards (Projects)<br>Wiki<br>Gists / Snippets | Merge Requests<br>Issues<br>Discussions<br>Issue Boards<br>Wiki<br>Snippets              | Pull Requests<br>Issues (Jira integration)<br>— (use Jira/Confluence)<br>Trello/Jira Boards integration<br>Wiki<br>Snippets |
| **CI/CD & Automation**             | Actions (CI/CD)<br>Dependabot (Dependency Updates)                                             | Pipelines (CI/CD)<br>Auto DevOps<br>Dependency Scanning<br>Feature Flags<br>Service Desk | Pipelines                                                                                                                   |
| **Package & Container Management** | Packages                                                                                       | Package Registry<br>Container Registry                                                   | Pipelines Artifacts (basic)                                                                                                 |
| **Static & Hosting**               | Pages (Static Sites)                                                                           | Pages                                                                                    | Static Sites                                                                                                                |
| **Integrated Dev Environment**     | Codespaces (Online IDE)                                                                        | Web IDE                                                                                  | — (use VS Code / Sourcetree)                                                                                                |
| **Marketplace & Apps**             | Marketplace                                                                                    | Marketplace                                                                              | Atlassian Marketplace                                                                                                       |
| **Integrations & Ecosystem**       | —                                                                                              | —                                                                                        | Tight Jira Integration<br>Trello Integration<br>Smart Commits<br>Sourcetree (GUI Git client)                                |
| **API & CLI**                      | API<br>CLI                                                                                     | API<br>CLI                                                                               | API<br>— (use Git + REST)                                                                                                   |
| **Mobile Access**                  | Mobile App                                                                                     | Mobile App                                                                               | Mobile App                                                                                                                  |
