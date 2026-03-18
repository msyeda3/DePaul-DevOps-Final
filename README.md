DePaul DevOps Final Project: Java Spring Boot CI/CD Pipeline
Project Overview
This project demonstrates a fully automated CI/CD pipeline for a Java Spring Boot application using Jenkins, Maven, and GitHub. The pipeline automates the lifecycle from code commit to security auditing and artifact archiving.

CI/CD Pipeline Stages
The pipeline is defined in a declarative Jenkinsfile and consists of the following automated stages:

Stage A - Build: Executes mvn clean compile to ensure the code is syntactically correct and ready for testing.

Stage B - Test: Runs unit tests using the Maven Surefire plugin to validate application logic.

Stage C - Parallel Scanning:

OWASP Dependency Check: Scans 3rd-party libraries for known vulnerabilities (CVEs).

Maven Dependency Audit: Displays available updates for project dependencies.

Security & Quality Reports
Upon completion, the pipeline archives key reports as build artifacts:

Dependency Check Report: Located in target/, this report identifies security risks in the supply chain.

JaCoCo Coverage Report: Located in target/site/jacoco/, providing a visual breakdown of code test coverage.

Identified Security Vulnerabilities (Build #28)
The latest automated scan identified critical vulnerabilities that require attention:

Log4j (CVE-2025-68161): Found in log4j-api-2.23.1.jar. Risk of remote exploitation.

Apache Tomcat: 16 vulnerabilities found in tomcat-embed-core-10.1.36.jar, including CVE-2025-31651 and CVE-2026-24734.

Resilience Strategy
To ensure pipeline stability in a restricted lab environment:

Artifact Archiving: Used built-in archiveArtifacts with allowEmptyArchive: true to bypass missing Jenkins plugins while still preserving critical security data.

Workspace Management: Implemented "Clean before checkout" to prevent artifact contamination between builds.
Blocker: The primary blocker was a java.lang.NoSuchMethodError when attempting to use the dependencyCheckPublisher DSL method. This indicated that the specific OWASP Jenkins plugin was not installed or enabled in the environment.
Resolution: I refactored the post block to use the standard archiveArtifacts method. By adding allowEmptyArchive: true, I ensured the pipeline would remain successful even if the scan report was not generated in the expected format, allowing for a successful final build status.
