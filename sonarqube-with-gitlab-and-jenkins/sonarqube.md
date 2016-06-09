####Sonarqube:

SonarQube is an open source platform for continuous inspection of code quality.

Install sonarqube-gitlab plugin and sonarqube-jenkins plugin to integrate it with jenkins and gitlab. We can use gitlab authentication plugin also to authenticate it with git users.

We can install various plugins for sonarqube from it's dashboard.

We can manage users, groups and it's permission from dashboard. It's analysis shows:
Files
Directories
Number of lines
complexity
code duplications, function etc.

There are three type of analysis in latest sonarqube:

There are 2 different paradigms for SonarQube analysis. You switch among the two modes using the sonar.analysis.mode analysis parameter with one of these 2 values:
publish - this is the default. This mode analyzes everything that's analyze-able for the languages in question and pushes the results to the server for processing.
issues (previously called preview) - is typically used to determine whether code changes are good enough to move forward with, E.G. merge into the Git master. Used for example to perform pull request analysis. This is not intended to be used by developers. They should prefer using SonarLint.

When you are integrating it with gitlab make a runner in gitlab-ci to build it at every commit. make a file in your project with .gitlab-ci.yml with following content
```
sonar:
  script:
      - /home/gitlab-runner/sonar-runner/bin/sonar-runner -Dsonar.host.url=https://sonarurl.com -Dsonar.login=admin -Dsonar.password=admin\
       -Dsonar.projectBaseDir=/home/gitlab-runner/builds/your project path -Dproject.settings=/home/gitlab-runner/builds/sonarpropertiesfilepath/sonar-project.properties\
       -Dsonar.devcockpit.userAutoMapping=sonar -Dsonar.analysis.mode=incremental -Dsonar.issuesReport.console.enable=true -Dsonar.gitlab.project_id=gitlabprojectname\
       -Dsonar.gitlab.commit_sha=$CI_BUILD_REF -Dsonar.gitlab.ref=$CI_BUILD_REF_NAME
```
incremental mode is only availabe before 5.x version of sonarqube.

There will be a file sonar-project.properties in your project which contains project key, project version, project name, module, module id etc.

####Scope of Analysis:
SonarQube can perform analysis on 20+ different languages. The outcome of this analysis will be quality measures and issues (instances where coding rules were broken). However, what gets analyzed will vary depending on the language:
On all languages, "blame" data will automatically be imported from supported SCM providers. Git and SVN are supported automatically. Other providers require additional plugins.
On all languages, a static analysis of source code is performed (Java files, COBOL programs, etc.)
A static analysis of compiled code can be performed for certain languages (.class files in Java, .dll files in C#, etc.)
A dynamic analysis of code can be performed on certain languages.

We can pull code on sonar server and manually can run sonar-runner on the project.

By default, only files that are recognized by a language plugin are loaded into the project during analysis. For example if your SonarQube instance has the Java and JavaScript plugins on board, all .java and .js files will be loaded, but .xml files will be ignored. However, it is possible to import all text files in the analysis encoding in a project by setting Settings > Exclusions > Files > Import unknown files to true. 

####Scanner Options:
```
usage: sonar-scanner [options]
 
 Options:
  -D,--define <arg>     Define property
   -e,--errors           Produce execution error messages
    -h,--help             Display help information
     -v,--version          Display version information
      -X,--debug            Produce execution debug output
      ```

 #### Functions of Sonarqube dashboard:


