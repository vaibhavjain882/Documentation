### Convert between packages (deb, rpm, tgz) Using Alien.

```

# Installation process for centos and ubuntu respectively-

yum install alien

sudo apt-get install alien

# Convert from tgz to deb-

alien --to-deb package.tgz

# Convert from rpm to deb -

alien --to-deb package.rpm

# Convert from deb to rpm

alien --to-rpm package.deb

# you can install packages after converting by using -i parameter like -

alien -i package.rpm

# above command will create rpm to deb and install it. By default alien creates deb packages.

```
