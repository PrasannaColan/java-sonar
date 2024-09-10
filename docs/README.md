Sonar Quality Gate Maven Plugin
Maven Central Quality Gate Status Coverage Black Duck Security Risk Maven Build

Check your SonarQube project if it passes its quality gate. If it doesn't, the plugin will fail the maven job.

There a three modes supported:

Mode	Description
integrated (recommended)	Run in conjunction with sonar-maven-plugin (supports branches and pull requests)
The required information for fetching the data will be read from a sonar-maven-plugin generated file in target
simple	Run stand-alone for a simple SonarQube project (no branches)
advanced	Run stand-alone for a SonarQube project with branch name or pull request
Prerequisites
Java 11
see section Why Java 11? for details
SonarQube (depends on mode)
integrated: 5.3+
simple: 5.4+
advanced: 7.7+
Usage
Include the plugin declaration in your pom.xml

<plugin>
  <groupId>io.github.r0bb3n</groupId>
  <artifactId>sonar-quality-gate-maven-plugin</artifactId>
  <version>1.3.0</version>
</plugin>
and execute the following command:

mvn sonar:sonar sonar-quality-gate:check
For full reference of the modes and parameters, please check the Usage and Goals page in the sonar-quality-gate-maven-plugin documentation.

Internals
Why Java 11?
TL;DR usage of HttpClient (java.net.http.HttpClient)

Thinking about also supporting Java 8 led to the conclusion, that this would require relying on at least one additional dependency (to maintain) for HTTP calls (e.g. Apache HttpClient), which is solved out-of-the-box in Java 11+ by simply using java.net.http.HttpClient. Therefore, I decided to not support Java 8.

Calling SonarQube Web API
Project Status
Get the quality gate status of a project.

Endpoint

api/qualitygates/project_status

URL Parameters

Parameter	Supported
projectKey	yes
branch	yes
pullRequest	yes
analysisId	yes
projectId	no
Documentation

on sonarcloud.io Web-API
in your SonarQube instance: <sonar.host.url>/web_api/api/qualitygates/project_status
Compute Engine Task (ceTask)
This endpoint is used to retrieve the analysis id of a prior sonar-maven-plugin run.

Endpoint

api/ce/task

URL Parameters

Parameter	Supported
id	yes
additionalFields	no
Documentation

on sonarcloud.io Web-API
in your SonarQube instance: <sonar.host.url>/web_api/api/ce/task
Maven calls
Release
prepare - manage pom.xml and create proper commits and tag and push to remote

mvn -B release:prepare -DdevelopmentVersion=1-SNAPSHOT -DreleaseVersion=<release version> -Dtag=v<release version>
perform - checkout version tag, create binaries and deploy via oss.sonatype.org to Maven Central and create documentation ("site") and push to gh-pages branch.

mvn -B release:perform -DreleaseProfiles=build-for-release
clean - remove backup/work file (useful if you ran prepare but not perform)

mvn -B release:clean
How-To release
Ensure the right git configs (username, email, signing stuff, ...) are set on --global level (correct .gitconfig file in user home) - config done on level --local is not respected by additional steps that take place in subdirectories of target with fresh full-checkouts of the repo (e.g. commits/push during site-deploy via maven-scm-publish-plugin).
Update CHANGELOG.md: add a section for the upcoming version and move all "unpublished" changes to it
Update README.md: replace all occurrences of previous version number with upcoming version
Persist: git add CHANGELOG.md README.md && git commit -m "prepare for release: update CHANGELOG.md/README.md" && git push
Create release in git repo: mvn -B release:prepare -DdevelopmentVersion=1-SNAPSHOT -DreleaseVersion=1.3.0 -Dtag=v1.3.0
Create and publish binaries and documentation: mvn -B release:perform -DreleaseProfiles=build-for-release
Create and push a tag for the site branch. Change into directory ./target/checkout/target/scmpublish-checkout and fire the according commands: git tag v1.3.0-site && git push origin v1.3.0-site
Create new release on GitHub (here)
choose tag: v1.3.0
set title: 1.3.0
copy the CHANGELOG.md content of the released version
upload the files ./target/checkout/target/*.(pom|jar|asc)
publish staging repository of oss nexus repository (login required) to maven central (overview doc / detailed doc)
check content of the staging repo
select staging repo
"Close" repo
evaluation is now running, see repo tab "Activity"
"Refresh" repo view
"Release" repo
copying is ongoing, see repo tab "Activity"
"Refresh" repo view, staging repo will disappear after successful copy process
verify that files are now available on the public release repo: GAV search
