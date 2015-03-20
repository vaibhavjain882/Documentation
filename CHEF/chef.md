###Chef:
Chef is one of the most popular configuration management tools. It uses Ruby and handles configuration by packing details into what it calls recipes.Chef provides a way to quickly deploy entire environments.Configuration management tools provide an avenue for deploying consistent, predictable code and configurations to a variety of client computers from a centralized management server.

###Chef Terminology:

####Chef Server:
This is the central location that stores configuration recipes, cookbooks, and node and workstation definitions.It handles connections and permissions from nodes and workstations and organizes data so that it can easily be pulled by clients. The server can also include a web interface in order to manage or configure some details.

####Chef Nodes:
Chef nodes are the deployment targets that are configured by Chef. Each node represents a separate, contained machine environment that can be on physical hardware or virtualized.Each node communicates with the central server using an application called chef-client. The chef-client program and the chef server communicate through the use of RSA key-based authentication.Chef-client uses a tool called ohai to get statistics about the node.

####Chef Workstations:
Chef workstations are where Chef configuration details are created or edited. The configuration files are then pushed to the Chef server, where they will be available to deploy to any nodes. An important tool to manage chef on a workstation is called knife.
Configurations and definitions that are created and modified on a workstation are committed to version control and then pushed to the server. The repository is called the chef-repo.

####Chef Repo File Structure:
In this directory, we can find a structure that looks like this:

1.certificates/: Contains the SSL certificates that can be associated with clients for authentication.

2.chefignore: Lists the files and directories within the structure that should not be included in the push to the server.

3.config/: Contains one of the two repository configuration files:
rake.rb: Defines some variable declarations for creating SSL certificates and some general options. 

4.cookbooks/: Contains the cookbooks that configure the infrastructure for your organization.

5.data_bags/:Data bags are protected sub-directories that contain sensitive configuration details. They are only accessible to those nodes that have matching SSL certificates and contain JSON formated files with configuration details.

6.environments/: Contains a top-level location to contain details for deploying the environment.
Every environment that diverges from the default environment must be defined in this directory.

7.Rakefile: This file defines the tasks that chef can perform in its configurations.

8.Roles/: Contains files that define the roles that can be assigned to nodes.

####Chef Cookbook File Structure:

1.The metadata.rb or metadata.json files contains metadata information about the service. This includes basic information like the name of the cookbook and the version, but it also is the place where the dependency information is stored. If this cookbook depends on other cookbooks to be installed, it can list them in this file and chef will install and configure them prior to the current cookbook.

2.The attributes directory contains attribute definitions that can be used to override or define settings for the nodes that will have this service.

3.The definitions directory contains files that declare resources. This means that you can group functionality together under one heading.

4.The files directory describes how chef should distribute files throughout the node on which this cookbook is deployed.

5.The recipes directory contains the "recipes" that define how the service should be configured. Recipes are generally small files that configure specific aspects of the larger system. If a cookbook used to install and configure a web server, a recipe may enable a module or set up a sane firewall default.

6.The templates directory is used to provide more complex configuration management. You can provide entire configuration files that contain embedded Ruby commands. The variables that are printed can be defined in other files.

### Installation and Configuration: Server
1.Download the rpm package (For Centos) and install it and then follow below steps:

```
$chef-server-ctl reconfigure

$chef-server-ctl user-create USERNAME FIRST_NAME LAST_NAME EMAIL PASSWORD -f USERNAME.pem

$chef-server-ctl org-create SHORTNAME LONGNAME --association_user USERNAME -f SHORTNAME-validator.pem

## SHORTNAME: your organization's short name
## LONGNAME: your organization's full name
```
#### Configure a Chef Workstation:
The actual infrastructure coordination and configuration does not take place on the Chef server. This work is done on a workstation which then uploads the data to the server to influence the Chef environment. Follow below steps:
```
Suppose your workstation is a MAC machine.
# Clone the Chef Repo from git

$git clone https://github.com/chef/chef-repo.git

# You can Push yor chef repo on git but here i am skipping this.

##Now, download and install a tool "knife" for MAC.

# Download the authenciation keys to the workstation from Server.

$scp root@server_domain_or_IP:/root/USERNAME.pem ~/chef-repo/.chef

$scp root@server_domain_or_IP:/root/SHORTNAME-validator.pem ~/chef-repo/.chef

## Now configure knife for your chef environment. open a knife.rb file in .chef directory and copy below content:

$ vi /chef-repo/.chef/knife.rb

current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "name_for_workstation"
client_key               "#{current_dir}/USERNAME.pem"
validation_client_name   "SHORTNAME-validator"
validation_key           "#{current_dir}/SHORTNAME-Validator.pem"
chef_server_url          "https://server_domain_or_IP/organizations/SHORTNAME"
syntax_check_cache_path  "#{ENV['HOME']}/.chef/syntaxcache"
cookbook_path            ["#{current_dir}/../cookbooks"]

## You will have to make /etc/chef/organizations/shortname directory ar chef server.

# Copy Chef server's SSL certificate on our workstation:

$ knife ssl fetch

# Now test your configuration by running following command:

$knife client list

# It will give you output like "SHORTNAME-validator".
```

### Bootstarpping a new node with knife:

With our Chef server and workstation configured, we can begin using Chef to configure new servers within our infrastructure.

This happens through a process called "bootstrapping" in which the Chef client executable is installed on the new computer and the organizational validator key is passed along as well. The new node then contacts the Chef server with the validator key and, in return, receives its own unique client key and any configuration that has been assigned to it. This process gets the new server into its initial state and sets it up for any future management.
```
$ knife bootstrap node_domain_or_IP -x root -P

# You should know the user which keys or password is setuped in the new node. In our case supose it is root.

Various option you can use with bootstrap command that are given below:

-x: Used to specify the username to authenticate with through SSH. This is usually required.

-N: The new name for the node, as displayed within Chef. Leaving this out will usually result in the hostname being used for the Chef node name.

-P: Used to specify the password for the username on the remote server. This is necessary if either the SSH 
session requires password authentication or if the username requires a password for sudo commands.

--sudo: If the username on the remote server will need to use sudo to perform administrative actions, this flag 
is needed. By default, it will prompt for the sudo password.

--use-sudo-password: If you are already providing the password for the user with the -P flag, using this flag in addition 
to the --sudo flag will use the -P password without prompting.

-A: This option forwards SSH keys to the remote host to login rather than using password authentication.

# You can run below command to test your configuration:

$ knife node list

# It will give you the new node name If your cobnfiguration is proper.

## Now, you have configured the workstation, chef server and node. Now we are going to make a cookbook
and test it in our chef environment.
```
### Create Simple Chef Cookbooks to Manage Infrastructure:

Cookbooks are usually used to handle one specific service, application, or functionality. Cookbooks are created on the workstation and then uploaded to a Chef server. 
Go to your chef repo directory and Create a simple cookbook for nginx:
```
$knife cookbook create nginx

$cd cookbooks/nginx

$cd recipes
$vi default.rb
## Copy below content in this file :
include_recipe "apt"
package 'nginx' do
  action :install
end

service 'nginx' do
  action [ :enable, :start ]
end

cookbook_file "/usr/share/nginx/www/index.html" do
  source "index.html"
  mode "0644"
end

#As you saw above, we defined a "cookbook_file" resource which should move a file called "index.html" 
into the document root on the node. We need to create this file.

$vi /chef-repo/cookbooks/nginx/files/default/index.html

# Copy below content just for testing purpose:
<html>
  <head>
    <title>Hello there</title>
  </head>
  <body>
    <h1>This is a test</h1>
    <p>Please work!</p>
  </body>
</html>

# You have to create a helper cookbook also like yum update before installing anything on your centos node server.

$knife cookbook create yum 
$vi /chef-repo/cookbooks/yum/recipes/default.rb

# copy below content in this file :

execute "yum update" do
  command "yum update"
end
 # Open the below file and copy given content:

$vi /chef-repo/cookbooks/nginx/metadata.rb

name             'nginx'
maintainer       'YOUR_COMPANY_NAME'
maintainer_email 'YOUR_EMAIL'
license          'All rights reserved'
description      'Installs/Configures nginx'
long_description IO.read(File.join(File.dirname(__FILE__), 'README.md'))
version          '0.1.0'

depends "yum"

## Now add your cookbooks to your node by erunning following command:

$knife cookbook upload yum

$knife cookbook upload nginx 

## Now, we have to modify the run-list of our nodes. We can do this easily by typing:  

$knife node edit name_of_node

but before editing you have to export your editor.

# you can see node name by typing command:
$knife node list

## Suppose name of node is node1 then we get a file like this:

{
  "name": "node1",
  "chef_environment": "_default",
  "normal": {
    "tags": [

    ]
  },
  "run_list": [

  ]
}

## Afetr adding your recipe nginx in this it looks like this:

{
  "name": "client1",
  "chef_environment": "_default",
  "normal": {
    "tags": [

    ]
  },
  "run_list": [
    "recipe[nginx]"
  ]
}
```

Now, ssh into your node manually or by knife ssh and run the command:

$ chef-client

Now access your node url if your configuration is right then you will find below content:

This is a test

Please work!

#### Roles and Environments:
In your organization, if your infrastructure grows to meet the demands of higher traffic, there are likely to be multiple, redundant servers that all perform the same basic tasks. For instance, these might be web servers that a load balancer passes requests to. They would all have the same basic configuration and could be said to each satisfy the same "role".
 A role in Chef is a categorization that describes what a specific machine is supposed to do. What responsibilities does it have and what software and settings should be given to it.


 An environment is simply a designation meant to help an administrator know what stage of the production process a server is a part of. Each server can be part of exactly one environment.
By default, an environment called "_default" is created.
For instance, one environment may be called "testing" and another may be called "production.

use environment-specific run_lists to specify variable configuration changes depending on which environment a server belongs to.

For instance, if a node is in the "production" environment, you could want to run a special recipe in your "nginx" cookbook to bring that server up to production policy requirements. You could also have a recipe in the nginx cookbook meant to configure special changes for testing servers.

Assuming that these two recipes are called "configprod" and "configtest" respectively. NOw..
#### Create a Role Using the Ruby DSL:
```
$ cd /chef-repo/roles

$ vi web.rb

# Copy below contents:
name "web"
description "A role to configure our front-line web servers"
run_list "recipe[yum]", "recipe[nginx]"
env_run_lists "production" => ["recipe[nginx::config_prod]"], "testing" => ["recipe[nginx::config_test]"]
default_attributes "nginx" => { "log_location" => "/var/log/nginx.log" }
override_attributes "nginx" => { "gzip" => "on" }
```
In the above example, we have specified that if the node is part of the production environment, it should run the "configprod" recipe within the "nginx" cookbook. However, if the node is in the testing environment, it will run the "configtest" recipe. If a node is in a different environment, then the default run_list will be applied.

Similarly, we can specify default and override attributes. You should be familiar with default attributes at this point. In our role, we can set default attributes which can override any of the default attributes set anywhere else.

We can also set override attributes, which have a higher precedence than many other attribute declarations. We can use this to try to force nodes that are assigned this role to behave in a certain way.
```
# Transferring Roles between the workstation and server:

$knife role from file path/to/role/file

# Assigning roles to nodes:

$knife node edit node1
## Just add 1 line of roles in this file then it looks like:

{
  "name": "client1",
  "chef_environment": "_default",
  "normal": {
    "tags": [

    ]
  },
  "run_list": [
    "role[web]"
  ]
}
```
This allows you to access all servers in a specific role by search. For instance, you could search for all of the database servers in your production environment by searching a role and environment:

```
knife search "role:database_server AND chef_environment:prod" -a name
```
### How to use Environments:

 They are also used to differentiate different servers, but instead of differentiating by the function of the server, environments differentiate by the phase of development that a machine belongs to.
In our "chef-repo" directory on our workstation, we should have an environments directory. This is where we should put our environment files.

```
$d ~/chef-repo/environments

$vi development.rb

## Copy below content in this:

name "development"
description "The master development branch"
cookbook_versions({
    "nginx" => "= 1.1.0",
    "apt" => "= 0.0.1"
})
override_attributes ({
    "nginx" => {
        "listen" => [ "80", "443" ]
    },
    "mysql" => {
        "root_pass" => "root"
    }
})

```
As you can see, one of the major advantages of incorporating environments into your system is that you can specify version constraints for the cookbooks, and recipes that are deployed.
We could upload our Ruby file to the Chef server by typing this:
```
knife environment from file ~/chef-repo/environments/development.rb
```

Now, set environments in Nodes.

```
$knife node edit node1

just add one line of chef_environment which is "default" by default. set it development. then
it looks like this:

 {
  "name": "client1",
  "chef_environment": "development",
  "normal": {
    "tags": [

    ]
  },
  "run_list": [
    "role[web_server]"
  ]
}
```
When you are done, save and close the file. On the next chef-client run on the node, it will pick up the new attributes and version constraints and modify itself to align with the new policy.

### Few other important commands of Knife:

####knife status: 
The status subcommand is designed to show status information about your nodes.

####knife ssh:
We can use knife ssh to issue commands to our nodes (or some subset of nodes) at once. For example, we can use knife ssh to restart Nginx on all our nodes with the role frontend.

```
knife ssh "role:web_server" "service nginx restart" -x yourusername -a ipaddress
```
#### Interactive knife ssh:
knife ssh also has the ability to put you into an interactive shell, where you can issue a series of commands and see the results very quickly. You can start an interactive knife ssh shell by using interactive in place of your SSH command.
```
knife ssh "role:web_server" interactive -x yourusername -a ipaddress
```
No you will get a knife shell from where you can run any command to multiple servers:

```
knife-ssh> uptime
```

Now, approximately all the basic tasks has been done.
You can manage windows server by using knife-windows tool and winrm. Ansible also provide support for windows server by some windows modules but it is not sufficient.In existing automation tools chef is good for managing windows server (said by powershell developer). Puppet is old and a liitle bit complex in configuration than chef. Chef and puppet both are agent/server and standalone also. 


