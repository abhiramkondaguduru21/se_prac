# Software Engineering Lab Internal-I — Exam Command & Scenario Reference

> **Purpose:** Last-minute practical reference for the KMIT Software Engineering Lab Internal-I exam.  
> **Primary focus:** Maven 40 + Git/GitHub 40 + Docker 20.  
> **Environment assumed:** Windows 11, JDK 17, Maven, Git for Windows/Git Bash, Eclipse, Apache Tomcat 9, Docker Desktop + WSL 2.
>
> The supplied question papers repeatedly use scenario-based tasks around Maven, Git/GitHub and Docker. Application names change, but the underlying commands and troubleshooting patterns are highly repetitive.

---

## 0. The Core Exam Mental Model

```text
                MAVEN
                  │
        build Java Web App
                  │
                  ▼
          target/*.war
                  │
          ┌───────┴────────┐
          ▼                ▼
       Tomcat            Docker
          │                │
          ▼                ▼
      localhost        Container
                           │
                           ▼
                       localhost
                           │
                           ▼
                       Docker Hub
```

### Five Git states to remember

```text
EDIT
  ↓
git diff
  ↓
WORKING DIRECTORY
  ↓
git add
  ↓
STAGING AREA
  ↓
git commit
  ↓
LOCAL REPOSITORY
  ↓
git push
  ↓
GITHUB
```

### Remote update distinction

```text
git fetch
→ download remote information; do NOT merge into current branch

git pull
→ fetch + integrate remote changes
```

### Image vs Container

```text
Dockerfile
   ↓ docker build
IMAGE
   ↓ docker run
CONTAINER
```

---

# 1. Windows Command Conventions

The college lab is Windows, so the primary command examples here use **Command Prompt (CMD)**.

### Windows navigation / inspection

```cmd
cd <path>                         :: change directory
cd ..                             :: go one directory up
dir                               :: list files/folders
dir target                        :: inspect target/
mkdir <folder>                    :: create folder
copy <source> <destination>      :: copy file
type <file>                       :: display text file
where git                         :: locate Git executable
where mvn                         :: locate Maven executable
where java                        :: locate Java executable
where docker                      :: locate Docker executable
```

### PowerShell difference

If using PowerShell and executing a `.bat` file from the current directory:

```powershell
.\catalina.bat version
.\startup.bat
```

In CMD:

```cmd
catalina.bat version
startup.bat
```

---

# 2. MAVEN — Environment & Project Identification

## Check Java and Maven

```cmd
java -version
javac -version
mvn -version
echo %JAVA_HOME%
```

### What each proves

| Command | Meaning |
|---|---|
| `java -version` | Java runtime available to the shell |
| `javac -version` | JDK compiler is installed |
| `mvn -version` | Maven version + the Java runtime Maven is actually using |
| `echo %JAVA_HOME%` | Configured JDK location |

### Java mismatch exam reflex

```text
java -version
     ↓
mvn -version
     ↓
compare the Java versions
     ↓
inspect pom.xml
     ↓
fix source/target or compiler plugin
     ↓
mvn clean package
```

---

# 3. MAVEN — `pom.xml` Essentials

A typical project identity is:

```xml
<groupId>KMIT</groupId>
<artifactId>VehicleRentalManagement</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>war</packaging>
```

### Meaning

```text
groupId
→ identifies the organization/project group

artifactId
→ identifies the artifact/project

version
→ version of the project

packaging
→ type of artifact Maven creates
```

Common values:

```xml
<packaging>jar</packaging>
```

or:

```xml
<packaging>war</packaging>
```

### Important defaults

If `<packaging>` is omitted, Maven's default packaging is:

```text
jar
```

For a Servlet/JSP application intended for Tomcat:

```xml
<packaging>war</packaging>
```

### `finalName`

```xml
<build>
    <finalName>VehicleRentalManagement</finalName>
</build>
```

Produces:

```text
target/VehicleRentalManagement.war
```

The WAR name normally determines the default Tomcat context path:

```text
/VehicleRentalManagement
```

Therefore:

```text
http://localhost:8080/VehicleRentalManagement/
```

---

# 4. MAVEN — Standard Project Structure

```text
project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   ├── resources/
│   │   └── webapp/          <- JSP/web resources for WAR project
│   └── test/
│       ├── java/
│       └── resources/
└── target/                  <- generated build output
```

### Key directories

```text
src/main/java
→ main Java source

src/main/resources
→ application resources/configuration

src/main/webapp
→ JSP/HTML/CSS/JS and web files

src/test/java
→ unit test source

target/
→ generated build output
```

---

# 5. MAVEN — Lifecycle Commands

```cmd
mvn clean
```

Deletes the previous `target/` build output.

```cmd
mvn validate
```

Checks that the project/POM is structurally valid.

```cmd
mvn compile
```

Compiles main source code.

```cmd
mvn test
```

Runs the test phase.

```cmd
mvn package
```

Builds the artifact:

```text
JAR → target/*.jar
WAR → target/*.war
```

```cmd
mvn install
```

Builds and installs the artifact into the local Maven repository:

```text
%USERPROFILE%\.m2\repository\
```

### Most useful combined commands

```cmd
mvn clean package
```

Fresh build and create JAR/WAR.

```cmd
mvn clean install
```

Fresh build and install artifact locally.

```cmd
mvn clean package -DskipTests
```

Build/package while skipping test execution.

```cmd
mvn clean package -X
```

Build with detailed Maven debug output.

---

# 6. MAVEN — Dependencies vs Plugins

## Dependency

A library that the application uses:

```xml
<dependencies>
    <dependency>
        <groupId>...</groupId>
        <artifactId>...</artifactId>
        <version>...</version>
    </dependency>
</dependencies>
```

Examples:

```text
Servlet API
MySQL Connector/J
JUnit
JSTL
Gson
```

## Plugin

A Maven tool used to perform build/lifecycle tasks:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>...</version>
        </plugin>
    </plugins>
</build>
```

Examples:

```text
maven-compiler-plugin
maven-war-plugin
maven-surefire-plugin
maven-clean-plugin
maven-install-plugin
maven-jar-plugin
maven-enforcer-plugin
```

### Exam trap

```text
dependency
→ application library

plugin
→ Maven build tool
```

---

# 7. MAVEN — Dependency Commands

## Show dependency tree

```cmd
mvn dependency:tree
```

Answers:

> What dependencies does this project resolve?

## Analyze dependencies

```cmd
mvn dependency:analyze
```

Useful for detecting unused or undeclared dependency usage.

## Inspect effective POM

```cmd
mvn help:effective-pom
```

Shows the effective configuration after inheritance, defaults, interpolation and profiles are applied.

## Inspect local repository

```cmd
dir "%USERPROFILE%\.m2\repository"
```

Open it in Explorer:

```cmd
explorer "%USERPROFILE%\.m2\repository"
```

Typical structure:

```text
C:\Users\<user>\.m2\repository\
    groupId-as-folders\
        artifactId\
            version\
                library-version.jar
```

---

# 8. MAVEN — Dependency Resolution

Maven resolves:

```text
direct dependencies
      +
transitive dependencies
      ↓
local repository / remote repository
      ↓
dependency graph
```

### Version conflict

If two dependencies require different versions of the same artifact, Maven applies dependency mediation.

Core exam rule:

```text
Nearest definition generally wins.
```

An explicitly declared direct dependency can be used to control the version selected.

### Diagnose a conflict

```cmd
mvn dependency:tree
```

Look for the same artifact appearing at different versions.

Then:

```text
identify conflicting versions
        ↓
choose compatible version
        ↓
explicitly declare desired version if appropriate
        ↓
mvn clean package
```

---

# 9. MAVEN — Dependency Failure Scenarios

## Scenario: "Required class cannot be found"

Typical symptom:

```text
package ... does not exist
cannot find symbol
class ... cannot be found
```

Do:

```cmd
mvn dependency:tree
```

Then inspect:

```text
pom.xml
```

Check:

```text
groupId
artifactId
version
scope
```

Then rebuild:

```cmd
mvn clean package
```

---

## Scenario: "The dependency is downloaded in `.m2`, so Maven should use it"

Wrong assumption.

A JAR existing in:

```text
%USERPROFILE%\.m2\repository
```

does not automatically make it a project dependency.

It must be correctly declared in:

```xml
<dependencies>
    ...
</dependencies>
```

Verify with:

```cmd
mvn dependency:tree
```

---

## Scenario: Dependency has no version

Incorrect/incomplete:

```xml
<dependency>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
</dependency>
```

Fix by providing a valid version, unless the version is intentionally supplied through dependency management.

---

## Scenario: Wrong coordinates

If Maven cannot resolve a library:

```text
Could not find artifact ...
```

Check:

```text
groupId
artifactId
version
repository availability
network connectivity
local cache
```

Then:

```cmd
mvn dependency:tree
mvn clean package
```

---

## Scenario: Wrong XML element

Incorrect:

```xml
<artificatId>...</artificatId>
```

Correct:

```xml
<artifactId>...</artifactId>
```

General reflex:

```text
POM parse/configuration error
        ↓
inspect XML spelling and nesting
        ↓
fix
        ↓
mvn validate
        ↓
mvn clean package
```

---

# 10. MAVEN — Compiler / Java Version Scenarios

## Scenario: Project must use Java 17

Simple configuration:

```xml
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
</properties>
```

Or configure the compiler plugin:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.13.0</version>
    <configuration>
        <source>17</source>
        <target>17</target>
    </configuration>
</plugin>
```

Then:

```cmd
java -version
mvn -version
mvn clean package
```

## Scenario: "Unsupported source/target version"

Use:

```cmd
java -version
javac -version
mvn -version
```

Compare the actual JDK with the POM's configured source/target.

Then correct:

```text
JAVA_HOME
PATH
Eclipse JRE
pom.xml compiler configuration
```

Then rebuild:

```cmd
mvn clean package
```

## Scenario: "No compiler is provided" / JRE rather than JDK

Check:

```cmd
java -version
javac -version
mvn -version
echo %JAVA_HOME%
```

The critical check is:

```text
javac must exist
```

A JRE alone does not provide the Java compiler.

---

# 11. MAVEN — WAR vs JAR

## Web application

```xml
<packaging>war</packaging>
```

Build:

```cmd
mvn clean package
```

Result:

```text
target/<finalName>.war
```

Used with:

```text
Apache Tomcat
```

## Standalone Java application

```xml
<packaging>jar</packaging>
```

Potential executable-JAR configuration:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.example.App</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

Run:

```cmd
java -jar target/<artifact>.jar
```

### Exam trigger

```text
WAR
→ web app
→ Tomcat

JAR
→ standalone app
→ java -jar
```

---

# 12. MAVEN — JUnit / Testing Scenarios

Run all tests:

```cmd
mvn test
```

Compiled tests:

```text
target/test-classes/
```

Surefire reports:

```text
target/surefire-reports/
```

Run one test class:

```cmd
mvn -Dtest=AppTest test
```

Automatically rerun failed tests:

```cmd
mvn test -Dsurefire.rerunFailingTestsCount=1
```

Skip test execution while packaging:

```cmd
mvn package -DskipTests
```

For stronger skipping of test compilation as well:

```cmd
mvn package -Dmaven.test.skip=true
```

---

# 13. MAVEN — Useful Special Scenarios

## Install a custom JAR into local repository

For a JAR not present in a public repository:

```cmd
mvn install:install-file ^
  -Dfile=library-utils.jar ^
  -DgroupId=com.example ^
  -DartifactId=library-utils ^
  -Dversion=1.0 ^
  -Dpackaging=jar
```

Then declare it normally in `pom.xml`.

## Multi-module project

Parent `pom.xml`:

```xml
<modules>
    <module>library-core</module>
    <module>library-web</module>
</modules>
```

Build all:

```cmd
mvn clean install
```

If `library-web` depends on `library-core`:

```text
library-core
    ↓
library-web
```

Maven builds `library-core` first.

---

# 14. MAVEN — "Build Succeeds But Website Does Not Open"

Never assume:

```text
BUILD SUCCESS
```

means:

```text
web application works
```

Use this isolation sequence:

```text
1. Verify artifact
        ↓
2. Verify Tomcat
        ↓
3. Verify WAR deployment
        ↓
4. Verify port
        ↓
5. Verify context path
        ↓
6. Verify JSP/Servlet/application
        ↓
7. Inspect logs
```

Commands:

```cmd
dir target
dir "%CATALINA_HOME%\webapps"
netstat -ano | findstr :8080
dir "%CATALINA_HOME%\logs"
```

Typical causes:

```text
wrong/missing WAR
wrong context path
Tomcat not running
port conflict
deployment failure
Servlet/JSP error
application configuration problem
```

---

# 15. TOMCAT — Windows Quick Reference

Assuming:

```text
CATALINA_HOME=C:\apache-tomcat-9.0.121
```

Check:

```cmd
echo %CATALINA_HOME%
```

Go to Tomcat:

```cmd
cd %CATALINA_HOME%\bin
```

Version:

```cmd
catalina.bat version
```

Start:

```cmd
startup.bat
```

Stop:

```cmd
shutdown.bat
```

Deploy WAR manually:

```cmd
copy target\VehicleRentalManagement.war "%CATALINA_HOME%\webapps\"
```

Verify:

```cmd
dir "%CATALINA_HOME%\webapps"
```

Expected:

```text
VehicleRentalManagement.war
VehicleRentalManagement\
```

Browser:

```text
http://localhost:8080/VehicleRentalManagement/
```

---

# 16. GIT — Repository Setup

Clone:

```cmd
git clone https://github.com/<username>/<repo>.git
```

Enter repository:

```cmd
cd <repo>
```

Verify files:

```cmd
dir
```

Verify Maven POM:

```cmd
dir pom.xml
```

Check status:

```cmd
git status
```

Check remote:

```cmd
git remote -v
```

### Set up an entirely new local repository

```cmd
git init
git status
git add .
git commit -m "Initial commit"
```

Connect remote:

```cmd
git remote add origin https://github.com/<username>/<repo>.git
git branch -M main
git push -u origin main
```

---

# 17. GIT — Branches

Create + switch:

```cmd
git switch -c feature/vehicle-update
```

Verify:

```cmd
git branch
```

All local + remote branches:

```cmd
git branch -a
```

Switch:

```cmd
git switch main
```

Older equivalent:

```cmd
git checkout -b feature/vehicle-update
```

### Critical

```text
git switch -c feature/name
→ local branch

git push -u origin feature/name
→ branch is published to GitHub
```

A local commit also remains local until pushed.

---

# 18. GIT — Status / Diff / Stage / Commit

Check current state:

```cmd
git status
```

See unstaged changes:

```cmd
git diff
```

See one file:

```cmd
git diff -- src\main\webapp\vehicle.jsp
```

Stage one file:

```cmd
git add src\main\webapp\vehicle.jsp
```

Verify staged state:

```cmd
git status
```

Commit:

```cmd
git commit -m "Update vehicle information"
```

Verify history:

```cmd
git log --oneline -5
```

Graph:

```cmd
git log --oneline --graph --all
```

### Best practice in exam scenarios

If the question asks for a specific file:

```cmd
git add <specific-file>
```

Do NOT automatically use:

```cmd
git add .
```

when unrelated IDE files are already modified.

---

# 19. GIT — Remote Synchronization

Show remote:

```cmd
git remote -v
```

Retrieve remote changes without integrating:

```cmd
git fetch origin
```

Fetch a specific remote branch:

```cmd
git fetch origin main
```

Fetch + integrate:

```cmd
git pull origin main
```

Pull using rebase:

```cmd
git pull --rebase origin main
```

Push current main:

```cmd
git push origin main
```

Push a new feature branch:

```cmd
git push -u origin feature/vehicle-update
```

### Exam distinction

```text
FETCH
→ see/download remote updates
→ current branch is NOT merged

PULL
→ fetch + integrate

PUSH
→ local commits → GitHub
```

---

# 20. GIT — Compare Branches

Content differences:

```cmd
git diff main..feature/vehicle-update
```

Changes introduced by feature since common ancestor:

```cmd
git diff main...feature/vehicle-update
```

Commits on feature but not main:

```cmd
git log main..feature/vehicle-update --oneline
```

Commits on main but not feature:

```cmd
git log feature/vehicle-update..main --oneline
```

Visual history:

```cmd
git log --oneline --graph --all
```

---

# 21. GIT — Merge

Update main:

```cmd
git switch main
git pull origin main
```

Merge feature:

```cmd
git merge feature/vehicle-update
```

If successful:

```cmd
git status
```

Push:

```cmd
git push origin main
```

Verify:

```cmd
git log --oneline --graph --all
git status
```

---

# 22. GIT — Merge Conflict

When merge reports a conflict:

```cmd
git status
```

Open the conflicted file.

Typical markers:

```text
<<<<<<< HEAD
current/main version
=======
feature version
>>>>>>> feature/vehicle-update
```

Resolve manually by keeping the correct final content.

Then:

```cmd
git add <conflicted-file>
git commit
```

Push:

```cmd
git push origin main
```

### Conflict reflex

```text
git status
→ identify file
→ edit/remove conflict markers
→ git add <file>
→ git commit
→ git push
```

---

# 23. GIT — Rebase

Rebase feature onto the latest main:

```cmd
git switch main
git pull origin main
git switch feature/vehicle-update
git rebase main
```

Meaning:

```text
Take feature commits
and replay them on top of latest main.
```

If conflict occurs:

```cmd
git status
```

Resolve the file, then:

```cmd
git add <file>
git rebase --continue
```

Abort the entire rebase:

```cmd
git rebase --abort
```

### Exam distinction

```text
merge
→ combines histories, may create merge commit

rebase
→ rewrites feature history so it is based on newer main
```

---

# 24. GIT — Undo / Recovery

## Unstage one file

```cmd
git restore --staged <file>
```

## Unstage everything

```cmd
git restore --staged .
```

Files are NOT deleted.

## Discard an uncommitted working-tree change

```cmd
git restore <file>
```

Caution: this discards the uncommitted modification.

## Recover an accidentally deleted uncommitted file

```cmd
git restore <file>
```

## Undo a commit while preserving commit history

```cmd
git revert <commit-id>
```

Creates a new commit that reverses the old one.

Best choice when the bad commit is already shared/pushed.

## Undo latest local commit and keep changes staged

```cmd
git reset --soft HEAD~1
```

## Undo latest local commit and keep changes in working tree, unstaged

```cmd
git reset HEAD~1
```

## Amend last commit message

```cmd
git commit --amend -m "Correct message"
```

### Exam decision rule

```text
Already pushed/shared?
        ↓
git revert

Local/unpushed history you are allowed to rewrite?
        ↓
git reset / git commit --amend
```

---

# 25. GIT — Stash

Temporarily store unfinished work:

```cmd
git stash
```

List stashes:

```cmd
git stash list
```

Restore the latest stash and remove it from stash list:

```cmd
git stash pop
```

Useful workflow:

```text
unfinished changes
       ↓
git stash
       ↓
switch branch
       ↓
do emergency work
       ↓
switch back
       ↓
git stash pop
```

No commit is created.

---

# 26. GIT — `.gitignore`

Typical Maven/Eclipse entries:

```gitignore
target/
.classpath
.project
.settings/
*.class
*.log
```

Check status:

```cmd
git status
```

If an already tracked file must stop being tracked but remain on disk:

```cmd
git rm --cached <file>
```

Then commit the change.

Example:

```cmd
git rm --cached src\main\resources\db-config.env
```

Do not confuse:

```text
git rm
→ remove from Git AND working tree

git rm --cached
→ remove from Git tracking, keep local file
```

---

# 27. GIT — Patches

Create a patch from one commit:

```cmd
git format-patch -1 <commit-id>
```

Check whether a patch can be applied:

```cmd
git apply --check <patch-file>
```

Apply patch to working tree:

```cmd
git apply <patch-file>
```

Apply a Git email-style patch as a commit:

```cmd
git am <patch-file>
```

### Patch reflex

```text
format-patch
→ create patch from commit

apply --check
→ test patch applicability

apply
→ apply changes

am
→ apply patch as commit
```

---

# 28. GIT — SSH / Remote Verification

Check remote:

```cmd
git remote -v
```

For SSH remote URLs, typical form:

```text
git@github.com:<username>/<repo>.git
```

Basic SSH test:

```cmd
ssh -T git@github.com
```

Use SSH when the question explicitly asks for SSH-based authentication/push.

---

# 29. DOCKER — Basic Commands

Check Docker:

```cmd
docker --version
docker compose version
docker info
```

List images:

```cmd
docker images
```

Running containers:

```cmd
docker ps
```

All containers:

```cmd
docker ps -a
```

Pull an image:

```cmd
docker pull ubuntu
```

Build an image:

```cmd
docker build -t vehiclerentalapp:latest .
```

Run:

```cmd
docker run -d -p 8080:8080 --name vehiclerental vehiclerentalapp:latest
```

Stop:

```cmd
docker stop vehiclerental
```

Start:

```cmd
docker start vehiclerental
```

Restart:

```cmd
docker restart vehiclerental
```

Remove container:

```cmd
docker rm vehiclerental
```

Force remove:

```cmd
docker rm -f vehiclerental
```

Remove image:

```cmd
docker rmi vehiclerentalapp:latest
```

---

# 30. DOCKER — Port Mapping

Syntax:

```text
-p HOST_PORT:CONTAINER_PORT
```

Example:

```cmd
docker run -d -p 8080:8080 ...
```

Means:

```text
Windows host :8080
       ↓
container   :8080
```

Another example:

```cmd
docker run -d -p 7070:8080 ...
```

Means:

```text
localhost:7070
       ↓
container:8080
```

### Classic exam trap

Do not reverse the order.

Correct:

```text
-p 7070:8080
```

NOT:

```text
-p 8080:7070
```

unless the question explicitly asks for that mapping.

---

# 31. DOCKER — Dockerfile for Maven WAR + Tomcat

A good exam template:

```dockerfile
# ---------- Stage 1: Build ----------
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package

# ---------- Stage 2: Runtime ----------
FROM tomcat:9-jdk17-temurin

COPY --from=build /app/target/*.war /usr/local/tomcat/webapps/

EXPOSE 8080

CMD ["catalina.sh", "run"]
```

The Maven and Tomcat image tags above are current official-image tags; `maven:3.9-eclipse-temurin-17` and `tomcat:9-jdk17-temurin` are listed by Docker Hub's official images.

### Dockerfile keywords

```text
FROM
→ base image

WORKDIR
→ working directory

COPY
→ copy files into image

RUN
→ execute build/setup command during image build

EXPOSE
→ document intended container port

CMD
→ default command when container starts
```

### Important

For classic `javax.servlet.*` applications, keep the runtime aligned with the Tomcat/Servlet generation used by the project. Do not blindly replace Tomcat 9 with Tomcat 10+ because the Servlet namespace changed.

---

# 32. DOCKER — Build Maven Web App Image

From the project root, where `Dockerfile` exists:

```cmd
docker build -t vehiclerentalapp:latest .
```

Verify:

```cmd
docker images
```

Expected conceptually:

```text
REPOSITORY          TAG       ...
vehiclerentalapp    latest    ...
```

---

# 33. DOCKER — Run Maven WAR Application

Before using host port 8080, remember:

```text
standalone Windows Tomcat on 8080
        +
Docker published on host 8080
        =
PORT CONFLICT
```

Stop the standalone Tomcat if necessary:

```cmd
%CATALINA_HOME%\bin\shutdown.bat
```

Check:

```cmd
netstat -ano | findstr :8080
```

Then:

```cmd
docker run -d -p 8080:8080 --name vehiclerental vehiclerentalapp:latest
```

Verify:

```cmd
docker ps
```

Browser:

```text
http://localhost:8080/VehicleRentalManagement/
```

---

# 34. DOCKER — Troubleshooting

## Container not visible under `docker ps`

Use:

```cmd
docker ps -a
```

It may have started and then stopped.

Check:

```cmd
docker logs vehiclerental
```

## Container exits immediately

```cmd
docker ps -a
docker logs vehiclerental
```

The logs usually reveal startup failure.

## Check port mapping

```cmd
docker port vehiclerental
```

## Detailed container configuration

```cmd
docker inspect vehiclerental
```

## Enter a running container

```cmd
docker exec -it vehiclerental /bin/sh
```

or:

```cmd
docker exec -it vehiclerental /bin/bash
```

## Check Tomcat deployment inside the container

```cmd
docker exec -it vehiclerental ls /usr/local/tomcat/webapps
```

Expected:

```text
VehicleRentalManagement.war
VehicleRentalManagement
```

---

# 35. DOCKER — 404 Not Found Scenario

If:

```text
http://localhost:8080/VehicleRentalManagement/
```

returns 404:

```text
1. Is container running?
       ↓
   docker ps

2. Was WAR copied?
       ↓
   docker exec ... ls /usr/local/tomcat/webapps

3. Was WAR deployed/extracted?
       ↓
   check VehicleRentalManagement directory

4. Is the context path correct?
       ↓
   WAR name / finalName

5. What does Tomcat say?
       ↓
   docker logs <container>
```

Common causes:

```text
wrong WAR name/context path
WAR missing
deployment failed
application/JSP error
wrong port
Tomcat startup problem
```

---

# 36. DOCKER — Logs

Normal logs:

```cmd
docker logs vehiclerental
```

Follow live logs:

```cmd
docker logs -f vehiclerental
```

Last 50 lines:

```cmd
docker logs --tail 50 vehiclerental
```

Exam reasoning:

```text
browser failure
   ↓
docker ps
   ↓
docker logs
   ↓
docker exec
   ↓
inspect deployment / configuration
```

---

# 37. DOCKER — Standalone Tomcat Container

Pull official Tomcat 9 Java 17 image:

```cmd
docker pull tomcat:9-jdk17-temurin
```

Run:

```cmd
docker run -d --name tomcat-server -p 7070:8080 tomcat:9-jdk17-temurin
```

Verify:

```cmd
docker ps
```

Copy WAR:

```cmd
docker cp target\VehicleRentalManagement.war tomcat-server:/usr/local/tomcat/webapps/
```

Verify:

```cmd
docker exec -it tomcat-server ls /usr/local/tomcat/webapps
```

Logs:

```cmd
docker logs tomcat-server
```

Browser:

```text
http://localhost:7070/VehicleRentalManagement/
```

This is different from a multi-stage Dockerfile:

```text
Standalone:
local Maven builds WAR
      ↓
docker cp
      ↓
Tomcat container

Multi-stage:
Docker builds WAR itself
      ↓
WAR copied internally
      ↓
Tomcat runtime stage
```

---

# 38. DOCKER — Docker Hub

Login:

```cmd
docker login
```

Tag:

```cmd
docker tag vehiclerentalapp:latest <username>/vehiclerentalapp:latest
```

Push:

```cmd
docker push <username>/vehiclerentalapp:latest
```

Verify locally:

```cmd
docker images
```

Then verify the repository on Docker Hub.

### Remember the image naming structure

```text
USERNAME/REPOSITORY:TAG
```

Example:

```text
abhi/vehiclerentalapp:latest
```

---

# 39. DOCKER — Ubuntu + Python Scenario

Pull Ubuntu:

```cmd
docker pull ubuntu
```

Run:

```cmd
docker run -dit --name python-container ubuntu
```

Enter:

```cmd
docker exec -it python-container bash
```

Inside container:

```bash
apt update
apt install -y python3
python3 --version
python3
```

Example:

```python
print("Hello from Docker")
```

Exit Python:

```python
exit()
```

Exit container shell:

```bash
exit
```

---

# 40. Scenario Bank — Maven

## Scenario: "Project must generate WAR"

```cmd
mvn clean package
```

Check:

```cmd
dir target
```

POM:

```xml
<packaging>war</packaging>
```

---

## Scenario: "Project generates JAR instead of WAR"

Check:

```xml
<packaging>war</packaging>
```

Then:

```cmd
mvn clean package
```

---

## Scenario: "Project builds with Java 17 on one machine but Java 8 on another"

```cmd
java -version
mvn -version
```

Then configure Java 17 in POM and rebuild:

```cmd
mvn clean package
```

---

## Scenario: "Maven cannot find a dependency"

```cmd
mvn dependency:tree
```

Then inspect:

```text
groupId
artifactId
version
scope
repository/network
.m2 cache
```

---

## Scenario: "Need detailed build diagnostics"

```cmd
mvn -X clean package
```

---

## Scenario: "Need to verify POM structure"

```cmd
mvn validate
```

---

## Scenario: "Need to see the effective configuration"

```cmd
mvn help:effective-pom
```

---

## Scenario: "Need to skip tests"

```cmd
mvn package -DskipTests
```

---

## Scenario: "Need one test class"

```cmd
mvn -Dtest=AppTest test
```

---

## Scenario: "Need WAR name `Food-System.war`"

```xml
<build>
    <finalName>Food-System</finalName>
</build>
```

Then:

```cmd
mvn package
```

Output:

```text
target/Food-System.war
```

---

## Scenario: "Servlet API is missing"

Inspect source/POM and add the appropriate Servlet API dependency for the Servlet/Tomcat generation used by the project.

Then:

```cmd
mvn dependency:tree
mvn clean package
```

Do not blindly mix `javax.servlet.*` and `jakarta.servlet.*` generations.

---

# 41. Scenario Bank — Git

## Scenario: "Clone the project"

```cmd
git clone <url>
cd <repo>
```

---

## Scenario: "Verify remote"

```cmd
git remote -v
```

---

## Scenario: "Check all changes"

```cmd
git status
```

---

## Scenario: "Create feature branch"

```cmd
git switch -c feature/vehicle-update
```

---

## Scenario: "Verify branch"

```cmd
git branch
```

---

## Scenario: "Inspect exact modifications"

```cmd
git diff -- src\main\webapp\vehicle.jsp
```

---

## Scenario: "Commit one specific file"

```cmd
git add src\main\webapp\vehicle.jsp
git commit -m "Update vehicle information"
git log --oneline -5
```

---

## Scenario: "Download remote changes without merging"

```cmd
git fetch origin
```

---

## Scenario: "Update current branch with remote main"

```cmd
git pull origin main
```

---

## Scenario: "Rebase feature onto updated main"

```cmd
git switch main
git pull origin main
git switch feature/vehicle-update
git rebase main
```

---

## Scenario: "Abort rebase"

```cmd
git rebase --abort
```

---

## Scenario: "Undo pushed/shared commit safely"

```cmd
git revert <commit-id>
git push
```

---

## Scenario: "Undo last local commit but keep changes staged"

```cmd
git reset --soft HEAD~1
```

---

## Scenario: "Keep unfinished work while switching branches"

```cmd
git stash
git switch <other-branch>
```

Restore later:

```cmd
git stash pop
```

---

## Scenario: "Remove tracked secret/config file but keep local copy"

```cmd
git rm --cached src\main\resources\db-config.env
```

Then add it to `.gitignore`.

---

## Scenario: "Recover deleted uncommitted file"

```cmd
git restore <file>
```

---

## Scenario: "Unstage file"

```cmd
git restore --staged <file>
```

---

## Scenario: "Compare feature and main"

```cmd
git diff main..feature/vehicle-update
git log main..feature/vehicle-update --oneline
```

---

## Scenario: "Merge feature into main"

```cmd
git switch main
git pull origin main
git merge feature/vehicle-update
git push origin main
```

---

# 42. Scenario Bank — Docker

## Scenario: "Create image"

```cmd
docker build -t vehiclerentalapp:latest .
docker images
```

---

## Scenario: "Run image on host port 8080"

```cmd
docker run -d -p 8080:8080 --name vehiclerental vehiclerentalapp:latest
docker ps
```

---

## Scenario: "Port 8080 is already occupied"

```cmd
netstat -ano | findstr :8080
```

Either stop the existing application or use another host port:

```cmd
docker run -d -p 7070:8080 --name vehiclerental vehiclerentalapp:latest
```

Then:

```text
http://localhost:7070
```

---

## Scenario: "Container doesn't appear to be running"

```cmd
docker ps
docker ps -a
docker logs vehiclerental
```

---

## Scenario: "Application returns 404"

```cmd
docker ps
docker logs vehiclerental
docker exec -it vehiclerental ls /usr/local/tomcat/webapps
```

Check:

```text
WAR exists?
WAR extracted?
context path correct?
Tomcat startup successful?
```

---

## Scenario: "Need to inspect container"

```cmd
docker inspect vehiclerental
```

---

## Scenario: "Need shell inside container"

```cmd
docker exec -it vehiclerental /bin/sh
```

---

## Scenario: "Push image to Docker Hub"

```cmd
docker login
docker tag vehiclerentalapp:latest <username>/vehiclerentalapp:latest
docker push <username>/vehiclerentalapp:latest
```

---

# 43. Full Vehicle Rental Exam Simulation

## Part I — Maven

```cmd
git clone https://github.com/nikithamoturi/VehicleRentalManagementSystem.git
cd VehicleRentalManagementSystem

java -version
mvn -version

mvn clean package
dir target

mvn dependency:tree
```

Inspect:

```text
pom.xml
src/main/webapp/
target/VehicleRentalManagement.war
```

Deploy to locally installed Tomcat:

```cmd
copy target\VehicleRentalManagement.war "%CATALINA_HOME%\webapps\"
```

Start Tomcat:

```cmd
%CATALINA_HOME%\bin\startup.bat
```

Verify:

```cmd
dir "%CATALINA_HOME%\webapps"
```

Browser:

```text
http://localhost:8080/VehicleRentalManagement/
```

---

## Part II — Git

```cmd
git status
git remote -v

git switch -c feature/vehicle-update
git branch

REM edit vehicle.jsp in Eclipse

git diff -- src\main\webapp\vehicle.jsp

git add src\main\webapp\vehicle.jsp
git commit -m "Update vehicle information"

git log --oneline -5
```

Second change:

```cmd
REM edit vehicle.jsp again

git diff -- src\main\webapp\vehicle.jsp
```

Remote synchronization:

```cmd
git fetch origin
git branch -a
```

Compare:

```cmd
git diff main..feature/vehicle-update
git log main..feature/vehicle-update --oneline
```

Merge:

```cmd
git switch main
git pull origin main
git merge feature/vehicle-update
git push origin main
git status
```

---

## Part III — Docker

Build:

```cmd
docker build -t vehiclerentalapp:latest .
```

Verify:

```cmd
docker images
```

Run:

```cmd
docker run -d -p 8080:8080 --name vehiclerental vehiclerentalapp:latest
```

Verify:

```cmd
docker ps
```

Logs:

```cmd
docker logs vehiclerental
```

Browser:

```text
http://localhost:8080/VehicleRentalManagement/
```

Inspect deployment:

```cmd
docker exec -it vehiclerental ls /usr/local/tomcat/webapps
```

Push:

```cmd
docker login
docker tag vehiclerentalapp:latest <username>/vehiclerentalapp:latest
docker push <username>/vehiclerentalapp:latest
```

---

# 44. Last-Minute Command Memory Sheet

## Maven

```cmd
mvn -version
mvn validate
mvn dependency:tree
mvn dependency:analyze
mvn help:effective-pom
mvn clean
mvn compile
mvn test
mvn package
mvn install
mvn clean package
mvn clean install
mvn clean package -DskipTests
mvn clean package -X
```

## Git

```cmd
git clone <url>
git status
git remote -v
git branch
git branch -a
git switch -c <branch>
git switch <branch>
git diff
git diff -- <file>
git add <file>
git commit -m "<message>"
git log --oneline
git log --oneline --graph --all
git fetch origin
git pull origin main
git push origin main
git diff main..feature
git log main..feature --oneline
git merge feature
git rebase main
git rebase --abort
git rebase --continue
git revert <commit>
git reset --soft HEAD~1
git restore <file>
git restore --staged <file>
git rm --cached <file>
git stash
git stash list
git stash pop
git format-patch -1 <commit>
git apply --check <patch>
git apply <patch>
git am <patch>
```

## Docker

```cmd
docker --version
docker info
docker images
docker ps
docker ps -a
docker pull <image>
docker build -t <name>:<tag> .
docker run -d -p <host>:<container> --name <name> <image>:<tag>
docker logs <container>
docker logs -f <container>
docker port <container>
docker inspect <container>
docker exec -it <container> /bin/sh
docker stop <container>
docker start <container>
docker restart <container>
docker rm <container>
docker rm -f <container>
docker rmi <image>
docker tag <image> <username>/<repo>:<tag>
docker login
docker push <username>/<repo>:<tag>
docker cp <source> <container>:<destination>
```

---

# 45. High-Value One-Line Answers

| Question | Answer |
|---|---|
| Maven Java compiler plugin | `maven-compiler-plugin` |
| Maven Java version | `mvn -version` |
| Maven debug | `mvn clean package -X` |
| Validate POM | `mvn validate` |
| Dependency tree | `mvn dependency:tree` |
| Dependency analysis | `mvn dependency:analyze` |
| Local Maven repository | `%USERPROFILE%\.m2\repository` |
| Remove old build | `mvn clean` |
| Compile | `mvn compile` |
| Test | `mvn test` |
| Package | `mvn package` |
| Install locally | `mvn install` |
| WAR packaging | `<packaging>war</packaging>` |
| JAR packaging | `<packaging>jar</packaging>` |
| WAR/JAR creation phase | `package` |
| Test classes | `target/test-classes/` |
| Surefire reports | `target/surefire-reports/` |
| Maven build output | `target/` |
| New Git branch | `git switch -c <branch>` |
| Check repository | `git status` |
| Check remote | `git remote -v` |
| Unstaged changes | `git diff` |
| Stage | `git add <file>` |
| Commit | `git commit -m "..."` |
| Remote-only download | `git fetch origin` |
| Fetch + integrate | `git pull` |
| Undo shared commit | `git revert <commit>` |
| Undo local commit, keep staged | `git reset --soft HEAD~1` |
| Abort rebase | `git rebase --abort` |
| Temporarily store work | `git stash` |
| Stop tracking, keep local file | `git rm --cached <file>` |
| Docker build | `docker build -t <name>:<tag> .` |
| Docker run | `docker run -d -p <host>:<container> <image>` |
| Running containers | `docker ps` |
| All containers | `docker ps -a` |
| Image list | `docker images` |
| Container logs | `docker logs <container>` |
| Container shell | `docker exec -it <container> /bin/sh` |
| Port mapping | `HOST:CONTAINER` |
| Docker Hub tag | `<username>/<repo>:<tag>` |
| Push image | `docker push <username>/<repo>:<tag>` |

---

# 46. The Three Troubleshooting Scripts

## Maven

```text
ERROR
 ↓
java -version
 ↓
mvn -version
 ↓
inspect pom.xml
 ↓
dependency:tree
 ↓
fix
 ↓
mvn clean package
```

## Git

```text
Unexpected repository state
 ↓
git status
 ↓
identify branch/files
 ↓
git diff / git log
 ↓
perform correct state transition
 ↓
git status
```

## Docker

```text
Browser failure
 ↓
docker ps
 ↓
docker ps -a
 ↓
docker logs <container>
 ↓
docker port <container>
 ↓
docker exec ... ls webapps/
 ↓
check WAR/context path/port
```

---

# 47. Final Exam Rules

```text
1. Read the scenario before choosing a command.

2. "Retrieve without modifying local branch"
   → git fetch

3. "Fetch and integrate"
   → git pull

4. "Already pushed/shared bad commit"
   → git revert

5. "Local unpushed bad commit"
   → reset/amend as appropriate

6. "Temporary unfinished work"
   → git stash

7. "WAR"
   → Tomcat

8. "JAR"
   → standalone Java / java -jar when executable

9. "BUILD SUCCESS"
   does NOT automatically mean
   "application works"

10. Docker port syntax is:
    HOST:CONTAINER

11. Image ≠ container.

12. commit ≠ push:
    commit → local repository
    push   → GitHub

13. Maven install ≠ Tomcat deployment:
    install → local .m2
    Tomcat  → runs WAR

14. When the question asks for one file,
    stage one file.

15. Never blindly use git add .
    when unrelated Eclipse files are modified.

16. When debugging, verify first; do not randomly change configuration.
```

---

# 48. Exam Workflow in One Screen

```text
GITHUB
  ↓
git clone
  ↓
ECLIPSE
  ↓
inspect pom.xml
  ↓
MAVEN
  ↓
mvn -version
mvn dependency:tree
mvn clean package
  ↓
target/*.war
  ↓
TOMCAT
  ↓
webapps/
  ↓
localhost:8080/<context>
  ↓
GIT
  ↓
status → branch → modify → diff
→ add → commit → fetch/pull → compare
→ merge/rebase → push
  ↓
DOCKER
  ↓
Dockerfile
  ↓
docker build
  ↓
docker run -d -p
  ↓
docker ps
docker logs
docker exec
  ↓
localhost
  ↓
Docker Hub
  ↓
tag → login → push
```

---

## Source/Scope Note

This reference is built around the supplied KMIT Internal-I question papers and lab material, with the CSE-B Vehicle Rental paper used as the main workflow model. The question papers repeatedly structure the practical around Maven project setup/troubleshooting, Git/GitHub operations, and Docker containerization. Application names and exact scenario wording vary between sets, while the technical operations remain substantially the same.

Current Docker official-image tags used in the example Dockerfile were checked against Docker Hub while preparing this reference.
