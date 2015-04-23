### Download omnibus package of gitlab and gitlab-ci-runner from official website:

I am going to install gitlab gitlab-ci and gitlab-ci-runner on one server.

```
wget https://downloads-packages.s3.amazonaws.com/centos-6.6/gitlab-7.9.4_omnibus.1-1.el6.x86_64.rpm

rpm -ivh gitlab-7.9.4_omnibus.1-1.el6.x86_64.rpm

wget https://s3-eu-west-1.amazonaws.com/downloads-packages/centos-7.0.1406/gitlab-runner-5.2.0~omnibus.1-1.x86_64.rpm

rpm -ivh gitlab-runner-5.2.0~omnibus.1-1.x86_64.rpm

```
Since your are going to run gitlab and gitlab-ci on same server so you have to make two entries for same ip in your DNS. Suppose your ip is "192.168.1.10" and two entries is "gitlab.example.com" and "gitlab-ci.example.com"

In your "/etc/gitlab/gitlab.rb" file make twoi entries for external_url and ci_external_url:

```
external_url	gitlab.example.com

ci_external_url	gitlab-ci.example.com

```

Now, run follwoing command:
```
gitlab-ctl reconfigure
```

Now, acess gitlab.example.com from browser. default username and password should be the following:

``
Username: root 
Password: 5iveL!fe
```
Change the root password and make another user "developer" and group "developers". Add "developer" user into "developers" group. Now, make an project called "project-1" and assign the group "developers" to it.

Now, clone your project "project-1" at your local machine and make some changes by follwing below steps:

```
1. Attach your key to user profile in gitlab.

2. Go to the project and copy the ssh url and run following command on your local machine :

    git clone ssh:XXXXXXXXXX

    Now, you may have to give your name and email before running any command.

Note: git should be installed on your local system.

3. Now, make a branch "developer" by below command on your local machine :

    git branch -b developer

4. Now write "hello world" in Readme.md and run below commands on local machine:

	git pull origin master   # You have to do it only once for first time    
	git add Readme.md
    	git commit -m "commit msg"
	git push origin master

5. Now, logout from root acess from GUI and login as user "developer"  change it's password , add a different key for user "developer" in user profile make another directory 
at your local system, go into that directory and follow same process you did recently for root. 
```

Now, access gitlab.example.com go to admin pannel click on new application enter application name and in url enetr follwing url:

```
http://gitlab-ci.example.com/user_sessions/callback
```
save it.

Now you will get app id and app secret copy it.

Now access gitlab-ci.example.com . Here you will get an option to login with gitlab credential. Login with your root user. Now Go to your gitlab server console open /etc/gitlab/gitlab.rb and make following entries:

```
gitlab_ci['gitlab_server'] = { "url" => 'http://gitlab.example.com', "app_id" => 'abcd', "app_secret" => 'xyz' }
```
replace above abcd and xyz with app id and app secret which you recently copied from gitlab dashboard. Run following command:

```
gitlab-ctl reconfigure
```
Now. In your browser refresh gitlab-ci.example.com which you recently accessed.

#### Your gitlab and gitlab-ci is okay and running. Now you have to integrate it with runners and make some builds.

Follow below steps:

1. Access gitlab-ci.example.com. Go to runners section. Copy given ci url and registration token. Go to your gitlab server console:

```
# Create the user that will run your builds
useradd -s /bin/false -m -r gitlab-runner

# Register your runner instance with a GitLab CI Coordinator
/opt/gitlab-runner/bin/setup -C /home/gitlab-runner

Here you will have to enter recently copied ci url and registartion token. Now your runner is registerd.

# Start the gitlab-runner

/opt/gitlab-runner/bin/runner -C /home/gitlab-runner/ > /dev/null &
```
2. Now, go to jobs section make a job you want CI to run on each push to repository. just create a job of following command:

```
service rsyncd restart
```
3. In "Deploy" job section make an acript like thie:

```
rsync --times --delete --stats --recursive -p -o /home/gitlab-runner/tmp/builds/project-1/ 192.168.1.233::check
```
But before 2nd and 3rd step you will have to follow my rsyncd documentation on gitlab server and all production servers. Link is:

```
https://github.com/srbhkmrsngh/Documentation/blob/master/Rsync/Rsync.md
```

4. Now make any changes in Readme.md push it to repository. Your build will run automatically and your code will be pushed to all production servers. 

You can make many deploy script such as for developer branch and for master branch and on every push at any branch your related build will run.




6.


