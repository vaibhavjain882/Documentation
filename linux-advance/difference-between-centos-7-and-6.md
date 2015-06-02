### Difference:

CentOS 7 introduces several major changes over the CentOS 6.x line. First, CentOS now uses XFS as its default file system. Second, OpenJDK-7 is now the default JDK. Third, “initd” has been replaced by “systemd”. Other changes include the use of the Linux Kernel 3.10.0, support for Linux Containers, and the inclusion of the Open VMware Tools and 3D graphics drivers out of the box.


#### In Detail:

#### DOCKER:
The biggest new addition to RHEL 7 is tight integration of Docker, the explosively popular application-virtualization technology. With Docker itself hitting 1.0 status, the timing on RHEL 7 couldn't be more fitting.

Apps packaged by Docker are isolated from the system and from each other, so they can be moved between systems and still run as expected. RHEL 7 is meant to be able to use Docker as efficiently as possible so that apps don't contend for resources or get confused about which edition of a runtime to use.

Long-term plans on the road map for Docker in RHEL involve possibly breaking the OS itself into a series of Docker containers, allowing as little or as much of a system to be deployed as needed with minimal overhead. Dubbed "Project Atomic," the initiative is still in the early stages, with Red Hat planning to deploy it first via its Fedora Linux distribution, nominally used as a testing ground for cutting-edge technologies.

#### Systemd:

The inclusion of the systemd process manager may spark controversy among system administrators and Linux mavens. Systemd was developed to replace the init system in use since the days of proprietary Unix, and it allows, for example, more efficient loading of services during the boot process.

With systemd as a potential sore spot, Red Hat has not rushed in to add it. Fedora has included systemd as a default since version 15, released in 2010, giving Red Hat good experience with how systemd behaves in the real world. Also, systemd isn't joining RHEL 7 arbitrarily, but as part of larger plans for the OS. Red Hat wants to enhance the way Docker containers are supported in RHEL 7 by using systemd, for example.

#### XFS By Default:

A third major change, though not likely to raise nearly as many eyebrows, applies to the default file system used by RHEL to XFS.

Originally created by Silicon Graphics International, XFS has long been in production use with Linux systems, and on RHEL 7 it'll support file systems of up to 500TB in size. RHEL 6 used ext4 as the default, although it shipped with XFS as an option. Red Hat competitor Suse Linux also supports XFS, although it defaults to ext3 on installation.

Unfortunately, there's no real way to migrate from other file systems currently in use on RHEL -- such as ext4 or btrfs-- other than backing up and restoring.

#### Microsoft-compatible identity management:
Even admins who aren't fans of Microsoft Windows have a grudging respect for Microsoft Active Directory. RHEL 7 improves the way RHEL deals with AD by adding two key new features. Cross-realm trusts can now be established between RHEL 7 and AD, so AD users can access resources on the Linux side without having to go through another sign-on step. The other big AD-related addition to RHEL 7, realmd, automates both the discovery of AD (or other Red Hat identity services) based on DNS information and the process of joining to it.

#### Performance Co-Pilot:
Performance tuning without live statistics is like driving with the windshield painted over, so RHEL 7 introduces a new performance-monitoring system PCP (Performance Co-Pilot), originally created by Silicon Graphics International but now available as part of RHEL 7. In addition to monitoring and recording system stats, PCP sports APIs and a tool set for making that data available to other subsystems, such as -- you guessed it -- the newly introduced systemd.

Another minor addition in this vein: new performance profiles. RHEL 6 already had performance profiles, which are ways to tune RHEL overall to meet specific usage scenarios. RHEL 7 not only defaults to a new profile that emphasizes maximum throughput performance, but includes another new default profile for balancing performance against energy savings.

#### Max Supported File size:

Centos7: 500TB
Centos6: 16TB

1. Red Hat Enterprise Linux 7 provides the latest Ruby version, 2.0.0 and Python 2.7.5.

2. Red Hat Enterprise Linux 7 features OpenJDK7 as the default Java Development Kit (JDK) and Java 7 as the default Java version.

3. Red Hat Enterprise Linux 7 supports 40 Gigabit network interface controllers (NICs) from multiple hardware partners. This provides support for 40 Gigabit Ethernet link speeds enabling faster network communication for applications and systems. Note that the ethtool utility will report interface link speeds up to 40Gb data rates.

4. Red Hat Enterprise Linux 7 now requires at least 1 GB of disk space to install. However, Red Hat recommends a minimum of 5 GB of disk space for all supported architectures.


