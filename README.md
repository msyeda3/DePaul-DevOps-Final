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

Task 3 — Concept Questions
These answers reflect the actual technical hurdles and configurations seen in Build #28.

When should we run parallel execution in Jenkins? What happens if there is a failure in one job?

Answer: Parallel execution should be used for independent tasks that do not rely on each other's output, such as running the "OWASP Dependency Check" and "Maven Dependency Audit" simultaneously to reduce total build time. If one job fails, the other branches continue to completion, but the overall stage will be marked as a failure unless error handling (like catchError) is used.

Why do we stash/unstash the Dependency-Check report?

Answer: Stashing is used to save files generated in one stage (or on one node) so they can be "unstashed" and used in a later stage, even if that stage runs in a different environment or a cleaned workspace.

What does catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') accomplish?

Answer: This command allows a specific stage to fail and be marked "Unstable" (yellow) without stopping the rest of the pipeline or failing the entire build (red).

Why is jacoco.xml useful for SonarQube?

Answer: jacoco.xml contains the raw code coverage data; SonarQube imports this file to display coverage percentages and highlight which lines of code lack unit tests in its dashboard.

What is the difference between archiving artifacts vs just “files existing in the workspace”?

Answer: Files in the workspace are temporary and are usually deleted when a new build starts or the "Clean before checkout" trigger runs. Archiving "promotes" these files to the Jenkins master server, making them permanent and accessible via the Jenkins UI after the build finishes.

If the build fails, what is the first place you look?

Answer: The Console Output. For example, when Build #26 failed, the console provided the specific NoSuchMethodError stack trace needed to identify the missing plugin.

Task 4 — Discussion of Challenges
Blocker: The primary blocker was a java.lang.NoSuchMethodError when attempting to use the dependencyCheckPublisher DSL method. This indicated that the specific OWASP Jenkins plugin was not installed or enabled in the environment.
Resolution: I refactored the post block to use the standard archiveArtifacts method. By adding allowEmptyArchive: true, I ensured the pipeline would remain successful even if the scan report was not generated in the expected format, allowing for a successful final build status.
