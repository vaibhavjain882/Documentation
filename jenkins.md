###Jenkins :
Jenkins is an open source continuous integration tool written in Java. You can install jenkins from your package manager easily. OpenjDK java should be installed on your server.

There is following options at Jenkins dashboard:
```
New items: you can make new project from here
manage jenkins: configure jenkins, manage plugins(Android, git email extension, ldap, oauth, credentials plugin etc.) ,
               jenkins cli, prepare to shutdown, manage users, global security (who is allowed to access the jenkins) etc.
project relationship: create relatipnship ntw two projects
check file fingerprint: to check file version
etc.
```
In Configure Jenkins options :
```
Home directory
global properties : Environment variables
jenkins url
git url
post build tasks : sending mail, executing scripts etc.
path to ssh keys: ssh servers
Parametrized build
```

How to set parametrized build: In project configuration choose this project is parametrized then add parameter strings and the branch name.

You can use poll scm option in jenkins project configuration to trigger a build automatically.


