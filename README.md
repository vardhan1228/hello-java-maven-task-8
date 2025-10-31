# CI/CD with Jenkins + Maven — quick start

This README shows how to use Jenkins to build a simple Java Maven project (your first step into CI/CD).

It assumes the project already contains a `pom.xml` and `src/` (as in this workspace).

## What you'll need (all free)

- Jenkins (instlled via Docker)
- Java JDK 17 (for local builds and agents if needed)
- Maven (optional locally — Jenkins can install Maven for jobs)

## Quick overview (what we'll do)

1. Start Jenkins (Docker is easiest).
2. Configure Jenkins → Manage Jenkins → Global Tool Configuration → Add Maven (example: Maven 3.8.6).
3. Create a Freestyle project that invokes the Maven goal `clean package`.
4. Save and Build → observe `BUILD SUCCESS` in Console Output.

## java install

```
winget install EclipseAdoptium.Temurin.17.JDK
```
---

## 1) Start Jenkins (Docker)

If you don't have Jenkins installed locally, Docker is the fastest way. In PowerShell run:

```powershell
# run Jenkins LTS on port 8080 (Windows PowerShell)
docker run -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts

```

Notes:
- First startup will take time as plugins initialize.
- The Jenkins web UI will be at: http://localhost:8080

If you prefer a native OS install, follow the official Jenkins docs for your OS.

## 2) Install prerequisites on your machine (optional)

If you want to run Maven locally or use a local agent, install:
- JDK 17and set JAVA_HOME
- Maven (or let Jenkins auto-install; but local `mvn` is handy)
- Git (if your source will come from a Git repo)

Check locally:

```powershell
java -version
mvn -v
git --version
```

## 3) Configure Maven in Jenkins

1. Open Jenkins UI → Manage Jenkins → Global Tool Configuration.
2. Find the "Maven" section and click "Add Maven".
3. Give it a name (e.g. `Maven 3.8.6`).
4. Check "Install automatically" and choose the Apache Maven version (or provide installation directory if you installed on the node).
5. Save.

This makes the Maven installation available to jobs by the name you provided.

## 4) Create a Freestyle job that builds the Maven project

1. From Jenkins dashboard, click `New Item`.
2. Enter a name (e.g. `hello-java-maven-build`) and select `Freestyle project` → OK.
3. (Optional) Under "Source Code Management" choose Git and provide your repo URL. If you don't use Git, you can leave this empty and use the workspace on the Jenkins node.
4. In the `Build` section click `Add build step` → `Invoke top-level Maven targets`.
   - In `Maven Version` choose the Maven installation name you configured (e.g. `Maven 3.8.6`).
   - In `Goals` enter: `clean package`
5. Save the job.

## 5) Build and verify

- Click `Build Now` for your job.
- Click the build number in the Build History and then `Console Output`.
- Look for the line containing `BUILD SUCCESS` (Maven prints it when the build finishes successfully):

Example expected snippet in the console:

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

If you see `BUILD SUCCESS`, Jenkins successfully ran `mvn clean package` and produced the artifact (usually in `target/`).


## Troubleshooting tips

- If `mvn` fails on Jenkins but works locally, check the Jenkins node's JDK and Maven configuration.
- If Jenkins can't find Maven, confirm the tool name in Global Tool Configuration matches what the job expects.
- For permission issues with Docker-mounted volumes, ensure the Jenkins user inside the container can read/write the workspace.
- Check plugin manager if you see UI options missing.


---

