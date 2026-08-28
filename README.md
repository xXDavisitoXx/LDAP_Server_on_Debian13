# LDAP_Server_on_Debian13

1 install software
```bash
apt update
apt install slapd ldap-utils
```

1.1
Create admin password 

2 Initialize LDAP wizard
```
sudo dpkg-reconfigure slapd
```
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
```bash
sudo systemctl restart slapd
sudo systemctl status slapd
```
2.8 Verify Domain name its correct
sudo  slapcat

3 
Create structure of LDAP
```bash
nano base.ldif
```

```ldif
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
```

3.1 Import structure to lDAP 
```bash
sudo ldapadd -x -D cn=admin,dc=computer,dc=academy,dc=com -W -f base.ldif
```

3.2 Check the base group is imported
```bash
sudo ldapsearch -x -b "dc=computer,dc=academy,dc=com" ou
```

