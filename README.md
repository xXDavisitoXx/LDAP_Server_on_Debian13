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
Create structure of LDdc=computer,dc=academy,dc=com

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

3.2 Check the base group is imported
```bash
sudo ldapsearch -x -b "dc=computer,dc=academy,dc=com" ou
```


4.0 Import sudoers schema to LDAP
El esquema sudo debe existir antes de importar cualquier LDIF que contenga objetos sudoRole, pero no depende de que hayas importado previamente base.ldif.

4.1
Download the Debian packet

```bash
mkdir sudo-schema
cd sudo-schema
apt download sudo-ldap
```

4.2 Extract the Debian packet
```bash
dpkg-deb -x sudo-ldap_*.deb extract
```

4.3 Import sudoers schema
```bash
ldapadd -Y EXTERNAL -H ldapi:/// -f extract/usr/share/doc/sudo-ldap/schema.olcSudo
```
:warning: if you dont find the schema in the extract you can search:

```bash
find extract -name "schema.olcSudo"
```

5 LAM 

5.1 install LAM

```bash
sudo apt install ldap-account-manager
```

http://LDAP-IP/lam

5.2 Update PHP memory limit to 256M
```bash
 nano /etc/php/8.4/apache2/php.ini
```
```bash
memory_limit = 256M
```
5.3 Securize IP range to connect 
```bash
 nano /etc/apache2/conf-enabled/ldap-account-manager.conf
```

```conf
#Require all granted
Require ip 127.0.0.1 192.168.10.0/24
```

5 Try web acces
