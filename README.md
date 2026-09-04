# LDAP_Server_on_Debian13

1 install software
```bash
apt update
apt install slapd ldap-utils (sudo or sudo-ldap)
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
Create structure of LDAP dc=computer,dc=academy,dc=com (Example)

```conf
dc=computer,dc=academy,dc=com

├── ou=Users
│   ├── ou=Active
│   ├── ou=Quarantine
│   └── ou=Services
│
├── ou=Groups
│   ├── ou=System
│   ├── ou=Applications
│   └── ou=NetworkGroups
│
├── ou=Machines
│   ├── ou=Servers
│   ├── ou=Clients
│   └── ou=Disabled
│
├── ou=Roles
│   ├── ou=Sudoers
│   ├── ou=LDAP
│   ├── ou=SSH
│   └── ou=Print
│
├── ou=Policies
│
├── ou=Certificates
│   ├── ou=CertificateAuthorities
│   ├── ou=Users
│   ├── ou=Machines
│   ├── ou=Services
│   └── ou=Revoked
│
└── ou=Resources
    ├── ou=Shared
    ├── ou=Printers
    ├── ou=Applications
    └── ou=Rooms
```

```bash
nano base.ldif
```

```conf
dn: dc=computer,dc=academy,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
dc: computer
o: Homelab

dn: ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Users

dn: ou=Active,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Active

dn: ou=Quarantine,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Quarantine

dn: ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Services

dn: ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Groups

dn: ou=System,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: System

dn: ou=Applications,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Applications

dn: ou=NetworkGroups,ou=Groups,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: NetworkGroups

dn: ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Machines

dn: ou=Servers,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Servers

dn: ou=Clients,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Clients

dn: ou=Disabled,ou=Machines,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Disabled

dn: ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Roles

dn: ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Sudoers

dn: ou=LDAP,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: LDAP

dn: ou=SSH,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: SSH

dn: ou=Print,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Print

dn: ou=Policies,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Policies

dn: ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Certificates

dn: ou=CertificateAuthorities,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: CertificateAuthorities

dn: ou=Users,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Users

dn: ou=Machines,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Machines

dn: ou=Services,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Services

dn: ou=Revoked,ou=Certificates,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Revoked

dn: ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Resources

dn: ou=Shared,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Shared

dn: ou=Printers,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Printers

dn: ou=Applications,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Applications

dn: ou=Rooms,ou=Resources,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: organizationalUnit
ou: Rooms
```

3.1 Import structure to lDAP 
```bash
sudo ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f base.ldif
```
⚠️ If import fails because to the first DN block erase this.

Check the base group is imported
```bash
sudo ldapsearch -x -b "dc=computer,dc=academy,dc=com" ou
```

3.2 Import sudoers or other schemas to LDAP
El esquema sudo debe existir antes de importar cualquier LDIF que contenga objetos sudoRole, pero no depende de que hayas importado previamente base.ldif.

3.2.1
Download the Debian packet

```bash
mkdir sudo-schema
cd sudo-schema
apt download sudo-ldap
```

3.2.2 Extract the Debian packet
```bash
dpkg-deb -x sudo-ldap_*.deb extract
```

3.2.3 Import sudoers schema
```bash
ldapadd -Y EXTERNAL -H ldapi:/// -f extract/usr/share/doc/sudo-ldap/schema.olcSudo
```
:warning: if you dont find the schema in the extract you can search:

```bash
find extract -name "schema.olcSudo"
```

3.3 Create Roles


```conf
# Roles.ldif

dn: cn=%Administrators-Linux,ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: sudoRole
cn: %Administrators-Linux
sudoUser: %Administrators-Linux
sudoHost: ALL
sudoCommand: ALL

dn: cn=%Administrators-LDAP,ou=Sudoers,ou=Roles,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: sudoRole
cn: %Administrators-LDAP
sudoUser: %Administrators-LDAP
sudoHost: ALL
sudoCommand: /usr/bin/ldapadd
sudoCommand: /usr/bin/ldapmodify
sudoCommand: /usr/bin/ldapdelete
sudoCommand: /usr/bin/ldapsearch
sudoCommand: /usr/bin/ldappasswd
sudoCommand: /usr/sbin/slapadd
sudoCommand: /usr/sbin/slapcat
sudoCommand: /usr/sbin/slapmodify
sudoCommand: /usr/sbin/slappasswd
sudoCommand: /bin/systemctl start slapd
sudoCommand: /bin/systemctl stop slapd
sudoCommand: /bin/systemctl restart slapd
sudoCommand: /bin/systemctl status slapd
```

3.4 Import Roles

```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Roles.ldif
```


3.5 Create Groups

```conf
dn: cn=Administrators-LDAP,ou=Grupos,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: Administrators-LDAP
gidNumber: 2001

dn: cn=Administrators-Linux,ou=Grupos,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: Administrators-Linux
gidNumber: 2002

dn: cn=SSH,ou=Grupos,dc=correodip,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: SSH
gidNumber: 2003

dn: cn=Wiki,ou=Grupos,dc=correodip,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: posixGroup
cn: Wiki
gidNumber: 2004
```
3.6 Import Groups

```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Groups.ldif
```

3.7 Create Users

Users.ldif

```conf
dn: uid=User-Example-1,ou=Active,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: User-Example-1
cn: User-Example-1
sn: User-Example-1
givenName: User-Example-1
uidNumber: 1001
gidNumber: 1001
homeDirectory: /home/User-Example-1
loginShell: /bin/bash
userPassword: {SSHA}VW5Vc3VhcmlvUGVyc29uYWwxU1NIQUhhc2g=
shadowLastChange: 0

dn: uid=User-Service-1,ou=Services,ou=Users,dc=computer,dc=academy,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
uid: User-Service-1
cn: User-Service-1
sn: User-Service-1
userPassword: {SSHA}QWx1bW5vMUtLUG9kQXNTRUhhc2hGaWN0aWNpbw==
uidNumber: 1002
gidNumber: 1002
homeDirectory: /nonexistent
loginShell: /sbin/nologin
```
3.8 Import Users 

```bash
ldapadd -x -D "cn=admin,dc=computer,dc=academy,dc=com" -W -f Users.ldif
```

4 install LAM 

4.1 Download Packet

```bash
sudo apt install ldap-account-manager
```

4.2 Update PHP memory limit to 256M
```bash
 nano /etc/php/8.4/apache2/php.ini
```
```bash
memory_limit = 256M
```
4.3 Securize IP range to connect 

```bash
 nano /etc/apache2/conf-enabled/ldap-account-manager.conf
```

```conf
#Require all granted
Require ip 127.0.0.1 192.168.10.0/24
```
4.4 Restart service Apache2

```conf
sudo systemctl restart apache2
```

4.5 Try web acces
http://LDAP-IP/lam

4.6 Click the menu LAM configuration on the top right.

4.7 Click Edit server profiles to modify the OpenLDAP profile.
User: lam
pass: lam

4.8 Change settings and LAM user password

On the Tool settings, input the domain name of your OpenLDAP server.
On the Security settings, select the login method as Fixed list and input the details admin user for the OpenLDAP server.
On the Profile password, input the new password and repeat.

⚠️ We recommnded change login method in server preferences to LDAP search

4.9 Edit users and groups directory

Next, click on the Account Types section the configure the following section:

On the Users section, input the default base domain for OpenLDAP users. In his case, the default suffix is People.
On the Groups section, input the default base domain for the group. In this case, the default other group is Groups.
Click Save to apply the changes.

5 Configure LAM 


