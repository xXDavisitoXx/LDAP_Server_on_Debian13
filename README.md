# LDAP_Server_on_Debian13


1 install software
apt install slapd ldap-utils

Create admin password 

2
sudo dpkg-reconfigure slapd
2.1
Select NO omit LDAP config

2.2
Check the Domain name

2.3
Check the organization  name

2.4 
Enter and repeat the admin password 

2.5 
No delete old database 

2.6 
Yes move old database

2.7  Restart and check service slapd
sudo systemctl restart slapd
sudo systemctl status slapd

2.8 Verify Domain name its correct
sudo  slapcat

3 
Create structure of LDAP

nano base.ldif

# base.ldif

dn: dc=computer,dc=acadenmy,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
dc: computer
o: Computer Academy of xXDavisitoXx

dn: ou=Usuarios,dc=computer,dc=acadenmy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Users

dn: ou=Groups,dc=computer,dc=acadenmy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Groups

dn: ou=Roles,dc=computer,dc=acadenmy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Roles

dn: ou=Services,dc=computer,dc=acadenmy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Services
