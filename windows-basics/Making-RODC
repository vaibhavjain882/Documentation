### Deploy a read-only domain controller in an existing domain:

Read-only domain controllers are ideal in remote location where system security cannot be guarenteed. They allow the remote site to have local authentication point, without storing vulnerable data about every object in the domain. The only information stored on a read-only domain controller is that of users and computers it has been authorized to authenticate. Any other object that is queried or authorized against is forwarded by the read-only DC to wrtiable domain controller.

	A newly deployed RODC will not authenticate users or computers. It will forward all authentication and access requests to writable domain controller. We must specific which users, groups, or computers the RODC will cache credentials for in order for authentication and access rights to be done by it.


The following steps will install the Active Directory role and promote the server to be a domain controller:

1. Launch Server Manager.
2. Click the Manage link at the top-right of the Server Manager console.
3. On the Before you begin screen, click Next.
4. On the Select installation type screen, ensure Role-based or feature-based installation is selected, and then click Next.
5. On the Select destination server screen, click Next.
6. On the Select server roles screen, select Active Directory Domain Services, and then click Next.
7. If Add Roles and Features Wizard dialog box appears, click Add Features.
8. On the Select features screen, click Next.
9. On the Active Directory Domain Services screen, Click Next.
10. On the Confirmation screen, ensure Restart the destination server automatically if required is checked, and then click Install.
11. When the installation completes, click Promote this server to a domain controller.
12. On the Deployment Configuration screen, ensure Add a domain controller to an existing domain is selected, enter the fully qualified domain name in the Domain text field, and add credentials for an administrator of the domain.
13. On the Domain Controller options screen, ensure Read only domain controller (RODC) is checked, select the site for the server by using the Site name drop-down, and set the DSRM password. When done, click Next.
		we are also installing the DNS role onto our RODC. When a DNS role is installed on a RODC, the DNS role also becomes read only. All DNS record registration requests submitted to this server will be forwarded to a full domain controller.

14. On the RODC Options screen, we can configure which accounts or groups are allowed to have their passwords replicated to the RODC. By default, a RODC replicates passwords of any account in the Allowed RODC Password Replication Group. This may be too global for our liking, so I’ve created a security group just for the Calgary users. The next step would be to remove the default Allowed RODC Password Replication group. Click Next when done.
		It’s important to remember that a read only domain controller will only cache credentials for accounts added to the security groups listed under Accounts that are allowed to replicate passwords to the RODC. IF you do not add accounts of the computers and users for this site, their passwords will not be stored on the RODC and they will not be able to log onto any computer or resources if the link between this site and a site with a writable domain controller goes down.

15. On the Additional Options screen, you may select which domain controller to replicate from or let that be decided automatically. Click Next when done.
16. On the Paths screen, you may select locations for the Active Directory database, log files, and SYSLOG files. A best practice is to move them onto a separate volume. When done, click Next.
17. On the Review Options screen, review your settings and then click Next.
18. On the Prerequisites Check screen, review the results and then click Install.
19. After the installation and replication of Active Directory objects completes, the server will reboot.
