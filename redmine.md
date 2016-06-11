###Redmine
Redmine is a free and open source, web-based project management and issue tracking tool. It allows users to manage multiple projects and associated subprojects. It features per project wikis and forums, time tracking, and flexible role based access control. It includes a calendar and Gantt charts to aid visual representation of projects and their deadlines. Redmine integrates with various version control systems and includes a repository browser and diff viewer.

####Features of Redmine:
```
Multiple projects support
Flexible role based access control
Flexible issue tracking system
Gantt chart and calendar
News, documents & files management
Feeds & email notifications
Per project wiki
Per project forums
Time tracking
Custom fields for issues, time-entries, projects and users
SCM integration (SVN, CVS, Git, Mercurial, Bazaar and Darcs)
Issue creation via email
Multiple LDAP authentication support
User self-registration support
Multilanguage support
Multiple databases support
```

#### Steps to configure Redmine
1. Install somedependencies:
```
yum -y install zlib-devel curl-devel openssl-devel httpd-devel apr-devel apr-util-devel mysql-devel 
```
2. Download and Install Ruby.
3. Download and install rubygems.
4. Install Passenger:
```
gem install passenger
yum install mod_passenger

A modern web server and application server for Ruby, Python and Node.js, optimized for performance, 
low memory usage and ease of use.
```
5. Download and install latest redmine from it's offcial website.
6. Install bundler and few gems which is required by redmne:
```
gem install bundler

Create New Gemfile and register these gems

vi /var/www/redmine/Gemfile
# file: /var/www/redmine/Gemfile
source "http://rubygems.org" 
gem "rake", "0.8.3" 
gem "rack", "1.1.0" 
gem "i18n", "0.4.2" 
gem "rubytree", "0.5.2", :require => "tree" 
gem "RedCloth", "~>4.2.3", :require => "redcloth" # for CodeRay
gem "mysql" 
gem "coderay", "~>0.9.7" 
bundle install
```

7. Create the redmine mysql database:
```
yum install mysql-server
chkconfig mysqld on
service mysqld start

start the mysql client (mysql -u root -p) and enter the following commands
create database redmine character set utf8;
create user 'redmine'@'localhost' identified by 'my_password';
grant all privileges on redmine.* to 'redmine'@'localhost'; 
```

8. Configure /var/www/redmine/config/database.yml :
```
Uncomment the following line in file redmine/config/environment.rb:
ENV['RAILS_ENV'] ||= 'production'
 
 Generate the session store
 RAILS_ENV=production bundle exec rake generate_session_store
  
  Migrate the database models
  RAILS_ENV=production bundle exec rake db:migrate
   
   Load default data (optional)
   RAILS_ENV=production bundle exec rake redmine:load_default_data
   Follow instructions.
    
    Rename  files in /var/www/redmine/public/
    mv dispatch.cgi.example dispatch.cgi
    mv dispatch.fcgi.example dispatch.fcgi
    mv dispatch.rb.example dispatch.rb
     
     Edit .htaccess file for CGI dispatch configuration
     mv htaccess.fcgi.example .htaccess
```

9. Edit httpd.conf
```
LoadModule passenger_module /usr/local/lib/ruby/gems/1.8/gems/passenger-3.0.17/ext/apache2/mod_passenger.so
PassengerRoot /usr/local/lib/ruby/gems/1.8/gems/passenger-3.0.17
PassengerRuby /usr/local/bin/ruby
PassengerTempDir /tmp/passenger
	  
<VirtualHost *:80>
ServerName xyz.com

# !!! Be sure to point DocumentRoot to 'public'!
DocumentRoot /home/redmine/redmine/public
 <Directory /home/redmine/redmine/public>

# This relaxes Apache security settings.
 AllowOverride all
 
 # MultiViews must be turned off
 Options -MultiViews
  </Directory>
  </VirtualHost>

									         
										  
Chown and Chmod files for read/write access for the Apache user
cd ..
chown -R apache:apache redmine-1.x
```
10. Start all services and use Redmine.

										  chmod -R 755 redmine-1.x 
										   
										 
