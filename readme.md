# Software Engineering Lab Internal --- Maven + Git + Docker Cheatsheet

> Quick command/reference sheet based on the SET-1 and SET-2 exam
> patterns discussed above.

------------------------------------------------------------------------

# 1. Git --- Clone / Repository Setup

## Clone repository

``` bash
git clone https://github.com/<username>/<repo>.git
```

SSH:

``` bash
git clone git@github.com:<username>/<repo>.git
```

Example:

``` bash
git clone https://github.com/deepthisagar7/AI-OLMS.git
cd AI-OLMS
```

Check files:

``` bash
ls
```

Check Maven project:

``` bash
ls pom.xml
```

Initialize a new repository:

``` bash
git init
git status
git add .
git commit -m "Initial commit"
```

Connect GitHub:

``` bash
git remote add origin https://github.com/<username>/<repo>.git
git remote -v
git branch -M main
git push -u origin main
```

------------------------------------------------------------------------

# 2. Git --- Branches

Create and switch:

``` bash
git switch -c feature/homepage
```

Example:

``` bash
git switch -c feature/course-recommendation
```

Older equivalent:

``` bash
git checkout -b feature/homepage
```

Check branches:

``` bash
git branch
```

Switch branch:

``` bash
git switch main
```

------------------------------------------------------------------------

# 3. Git --- Add / Commit / Status

``` bash
git status
git add .
git commit -m "Add homepage and README"
```

Create README:

``` bash
touch README.md
```

Check commit history:

``` bash
git log --oneline
git log --oneline --graph --all
```

Correct the latest unpushed commit message:

``` bash
git commit --amend -m "Added Assignment Module"
```

------------------------------------------------------------------------

# 4. Git --- Pull / Rebase / Merge

Get latest main changes without losing local commits:

``` bash
git pull --rebase origin main
```

Rebase feature branch onto main:

``` bash
git switch main
git pull origin main
git switch feature/homepage
git rebase main
```

Merge feature into main:

``` bash
git switch main
git pull origin main
git merge feature/homepage
git push origin main
```

------------------------------------------------------------------------

# 5. Git --- Undo / Recover

Recover deleted uncommitted file:

``` bash
git restore course.jsp
```

Unstage everything without deleting files:

``` bash
git restore --staged .
```

Unstage one file:

``` bash
git restore --staged <file>
```

Remove tracked file but keep it locally:

``` bash
git rm --cached <file>
```

Undo a commit while keeping the commit in history:

``` bash
git revert <commit-id>
```

Undo latest local commit while keeping changes staged:

``` bash
git reset --soft HEAD~1
```

Amend latest commit:

``` bash
git commit --amend
```

------------------------------------------------------------------------

# 6. Git --- Merge Conflict

Start merge:

``` bash
git merge main
```

Identify conflict:

``` bash
git status
```

Open the conflicted file and resolve:

``` text
<<<<<<< HEAD
your changes
=======
main branch changes
>>>>>>> main
```

Delete the conflict markers and keep the correct code.

Stage:

``` bash
git add course.jsp
```

Complete merge:

``` bash
git commit -m "Resolve merge conflict in course.jsp"
```

Push:

``` bash
git push origin feature/course-recommendation
```

------------------------------------------------------------------------

# 7. Git --- Compare Branches

Code differences:

``` bash
git diff main...feature/homepage
```

Commits in feature but not main:

``` bash
git log main..feature/homepage
```

Commits in main but not feature:

``` bash
git log feature/homepage..main
```

Visual history:

``` bash
git log --oneline --graph --all
```

------------------------------------------------------------------------

# 8. Git --- .gitignore

Typical Maven/Eclipse `.gitignore`:

``` gitignore
target/
.classpath
.project
.settings/
*.class
*.log
```

Create:

``` bash
touch .gitignore
```

Add and commit:

``` bash
git add .gitignore
git commit -m "Add gitignore"
```

If a file is already tracked:

``` bash
git rm --cached <file>
```

Then add the file/pattern to `.gitignore`.

------------------------------------------------------------------------

# 9. Maven --- Check Java and Maven

Check installed Java:

``` bash
java -version
```

Check Java compiler:

``` bash
javac -version
```

Check Maven and the Java Maven is using:

``` bash
mvn -version
```

Check JAVA_HOME:

``` bash
echo $JAVA_HOME
```

Windows Git Bash example for Java 17:

``` bash
export JAVA_HOME="/c/Program Files/Java/jdk-17"
export PATH="$JAVA_HOME/bin:$PATH"
```

Verify:

``` bash
java -version
mvn -version
```

------------------------------------------------------------------------

# 10. Maven --- Create a JAR Project

Generate a Maven project:

``` bash
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Enter:

``` bash
cd myapp
```

Build:

``` bash
mvn clean package
```

Output:

``` text
target/myapp-1.0-SNAPSHOT.jar
```

JAR packaging:

``` xml
<packaging>jar</packaging>
```

`jar` is Maven's default packaging.

------------------------------------------------------------------------

# 11. Maven --- WAR Project

For a web application:

``` xml
<packaging>war</packaging>
```

Build:

``` bash
mvn clean package
```

Output:

``` text
target/AI-OLMS.war
```

------------------------------------------------------------------------

# 12. Maven --- Java 17 WAR `pom.xml`

Relevant corrected structure:

``` xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>AI-OLMS</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>war</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <build>
        <finalName>AI-OLMS</finalName>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.13.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.4.0</version>
            </plugin>
        </plugins>
    </build>
</project>
```

------------------------------------------------------------------------

# 13. Maven --- Executable JAR

Change:

``` xml
<packaging>war</packaging>
```

to:

``` xml
<packaging>jar</packaging>
```

Configure `maven-jar-plugin`:

``` xml
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

Main class must contain:

``` java
public static void main(String[] args) {
    // application code
}
```

Build:

``` bash
mvn clean package
```

------------------------------------------------------------------------

# 14. Maven --- Lifecycle

``` text
clean   -> deletes target/
compile -> compiles source code
test    -> runs tests
package -> creates JAR/WAR
install -> installs package into local .m2 repository
```

WAR generation:

``` bash
mvn package
```

Clean + build:

``` bash
mvn clean package
```

Detailed debugging:

``` bash
mvn clean package -X
```

------------------------------------------------------------------------

# 15. Maven --- Dependencies

Show dependency tree:

``` bash
mvn dependency:tree
```

Search dependency:

``` bash
mvn dependency:tree | grep <dependency-name>
```

Check local Maven repository:

``` bash
ls ~/.m2/repository
```

Find JARs:

``` bash
find ~/.m2/repository -name "*.jar"
```

Find a particular library:

``` bash
find ~/.m2/repository -name "*library-name*.jar"
```

Maven normally resolves conflicting dependency versions using dependency
mediation; the nearest dependency in the dependency tree generally wins.

------------------------------------------------------------------------

# 16. Maven --- JUnit / Tests

Run tests:

``` bash
mvn test
```

Compiled test classes:

``` text
target/test-classes/
```

JUnit/Surefire reports:

``` text
target/surefire-reports/
```

Run one test class:

``` bash
mvn -Dtest=AppTest test
```

Rerun failing tests:

``` bash
mvn test -Dsurefire.rerunFailingTestsCount=1
```

------------------------------------------------------------------------

# 17. Maven --- Java Version Failure

Check:

``` bash
java -version
javac -version
mvn -version
```

If Maven is using the wrong JDK, fix `JAVA_HOME` and `PATH`, then
verify:

``` bash
mvn -version
```

Java 17 compiler configuration:

``` xml
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
</properties>
```

Then:

``` bash
mvn clean package
```

------------------------------------------------------------------------

# 18. Maven --- Require Java 17

Add Maven Enforcer Plugin:

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <version>3.5.0</version>
    <executions>
        <execution>
            <id>enforce-java</id>
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                    <requireJavaVersion>
                        <version>17</version>
                    </requireJavaVersion>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

------------------------------------------------------------------------

# 19. Docker --- Basic Commands

Check Docker:

``` bash
docker --version
docker info
```

List images:

``` bash
docker images
```

List running containers:

``` bash
docker ps
```

List all containers:

``` bash
docker ps -a
```

Build image:

``` bash
docker build -t ai-olms:latest .
```

Run container:

``` bash
docker run -d -p 7012:8080 ai-olms:latest
```

Stop:

``` bash
docker stop <container_id>
```

Start:

``` bash
docker start <container_id>
```

Restart:

``` bash
docker restart <container_id>
```

Remove:

``` bash
docker rm <container_id>
```

Force remove:

``` bash
docker rm -f <container_id>
```

Remove image:

``` bash
docker rmi <image>
```

------------------------------------------------------------------------

# 20. Docker --- Troubleshooting

Logs:

``` bash
docker logs <container_id>
```

Follow logs:

``` bash
docker logs -f <container_id>
```

Inspect:

``` bash
docker inspect <container_id>
```

Check port mapping:

``` bash
docker port <container_id>
```

Enter container:

``` bash
docker exec -it <container_id> bash
```

Check Tomcat webapps:

``` bash
docker exec -it <container_id> ls /usr/local/tomcat/webapps
```

------------------------------------------------------------------------

# 21. Dockerfile --- Maven + Java 17 + Tomcat

For a Maven WAR application:

``` dockerfile
# Stage 1: Build
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM tomcat:10.1-jdk17

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=build /app/target/*.war /usr/local/tomcat/webapps/

EXPOSE 8080

CMD ["catalina.sh", "run"]
```

Build:

``` bash
docker build -t ai-olms:latest .
```

Run:

``` bash
docker run -d -p 7012:8080 ai-olms:latest
```

Check:

``` bash
docker ps
docker logs <container_id>
```

------------------------------------------------------------------------

# 22. Docker --- Tomcat Standalone

Pull Tomcat:

``` bash
docker pull tomcat:10.1-jdk17
```

Run Tomcat:

``` bash
docker run -d --name tomcat-server -p 7070:8080 tomcat:10.1-jdk17
```

Verify:

``` bash
docker ps
```

Copy WAR:

``` bash
docker cp target/AI-OLMS.war tomcat-server:/usr/local/tomcat/webapps/
```

Verify WAR:

``` bash
docker exec -it tomcat-server ls /usr/local/tomcat/webapps
```

Check logs:

``` bash
docker logs tomcat-server
```

Open:

``` text
http://localhost:7070/AI-OLMS
```

If WAR is named `ROOT.war`:

``` text
http://localhost:7070
```

------------------------------------------------------------------------

# 23. Docker --- 404 Troubleshooting

Check deployed files:

``` bash
docker exec -it <container_id> ls -l /usr/local/tomcat/webapps
```

Check extracted application:

``` bash
docker exec -it <container_id> ls /usr/local/tomcat/webapps/AI-OLMS
```

Check logs:

``` bash
docker logs <container_id>
```

Possible causes:

``` text
1. Wrong WAR context path
2. WAR not copied into webapps
3. WAR deployment failed
4. Application/web configuration problem
```

For:

``` text
AI-OLMS.war
```

try:

``` text
http://localhost:7012/AI-OLMS
```

------------------------------------------------------------------------

# 24. Docker --- Ubuntu + Python

Pull Ubuntu:

``` bash
docker pull ubuntu
```

Run:

``` bash
docker run -dit --name python-container ubuntu
```

Enter:

``` bash
docker exec -it python-container bash
```

Inside container:

``` bash
apt update
apt install -y python3
python3 --version
```

Run Python:

``` bash
python3
```

Example:

``` python
print("Hello from Docker")
```

Exit:

``` python
exit()
```

Then:

``` bash
exit
```

------------------------------------------------------------------------

# 25. Docker Hub --- Push Image

Login:

``` bash
docker login
```

Tag:

``` bash
docker tag ai-olms:latest <dockerhub-username>/ai-olms:latest
```

Example:

``` bash
docker tag ai-olms:latest sreearnav/ai-olms:latest
```

Push:

``` bash
docker push sreearnav/ai-olms:latest
```

For OLES image:

``` bash
docker tag oles:latest sreearnav/oles:latest
docker push sreearnav/oles:latest
```

Verify locally:

``` bash
docker images
```

Then verify the repository on Docker Hub.

------------------------------------------------------------------------

# 26. Docker --- Cleanup

Remove unused containers/images:

``` bash
docker system prune
```

More aggressive:

``` bash
docker system prune -a
```

------------------------------------------------------------------------

# 27. Full Exam Workflow --- Maven WAR + Docker

``` bash
# Clone
git clone https://github.com/<username>/<repo>.git
cd <repo>

# Check Java/Maven
java -version
mvn -version

# Build WAR
mvn clean package

# Check WAR
ls target/

# Build Docker image
docker build -t ai-olms:latest .

# Run
docker run -d -p 7012:8080 ai-olms:latest

# Verify
docker ps
docker logs <container_id>
docker exec -it <container_id> ls /usr/local/tomcat/webapps

# Test
# http://localhost:7012/AI-OLMS

# Docker Hub
docker login
docker tag ai-olms:latest <username>/ai-olms:latest
docker push <username>/ai-olms:latest
```

------------------------------------------------------------------------

# 28. Full Exam Workflow --- Git

``` bash
git clone <ssh-or-https-url>
cd <repo>

git status
git remote -v

git switch -c feature/homepage

touch README.md
git add .
git commit -m "Add homepage and README"

git pull --rebase origin main

git switch main
git pull origin main
git switch feature/homepage
git rebase main

git diff main...feature/homepage
git log main..feature/homepage

git switch main
git merge feature/homepage
git push origin main

git status
```

------------------------------------------------------------------------

# 29. Important One-Line Answers

  Question                           Answer
  ---------------------------------- ---------------------------------------
  Maven Java compiler plugin         `maven-compiler-plugin`
  Maven Java version                 `mvn -version`
  Maven debug                        `mvn clean package -X`
  Build project                      `mvn clean package`
  WAR packaging                      `<packaging>war</packaging>`
  JAR packaging                      `<packaging>jar</packaging>`
  WAR generated by                   `package` phase
  Test classes                       `target/test-classes/`
  JUnit reports                      `target/surefire-reports/`
  Dependency tree                    `mvn dependency:tree`
  New branch                         `git switch -c <branch>`
  Undo pushed commit                 `git revert <commit>`
  Recover deleted file               `git restore <file>`
  Unstage files                      `git restore --staged .`
  Keep file locally, stop tracking   `git rm --cached <file>`
  Reorganize feature with main       `git rebase main`
  Merge feature                      `git merge feature/homepage`
  Docker build                       `docker build -t <name>:<tag> .`
  Docker run                         `docker run -d -p 7070:8080 <image>`
  Docker logs                        `docker logs <container>`
  Enter container                    `docker exec -it <container> bash`
  Push Docker image                  `docker push <username>/<repo>:<tag>`
