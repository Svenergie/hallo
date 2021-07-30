
Directory-Concept
========

Introduction
--------

The Central User Repository from TJVI is designed to authenticate
OS-Users on various Platforms. The goal is to operate a central and
standardized LDAP-Server that can be used by many Service-Platforms to
authenticate the Unix-Users. This central Services is provisioned
using the central OSS Identity Management System, which allows to reduce
the administrative tasks regarding users and groups significantly.

Architecture
--------

The following picture shows the overall architecture of the central
LDAP-Service of TJVI. The accounts will be automatically created by the
Identity Management System (IDM).

![LDAP Architecture Overview](img/LDAP-Architecture-Overview.png)

Attributes known to the IDM will be automatically filled in the
LDAP-Service. The Service is a central storage for accounts and
their attributes that can be used by the Service-Platforms.

The Server allows the Service-Platforms to authenticate using LDAP-Bind
with username and password. The Bind will be successful, if the account
is known to the service and the password is correct and valid. The
communication between the LDAP-Client and the LDAP-Service will be
encrypted using SSL with Server-Side-Certificates.

The Authorization-Part of the service supports Posix-Groups and Netgroups
for Operating Systems and GroupOfNames for other Applications.

The cluster of two servers is located in Ratingen (ossldap.oum.vfd2.de,
10.211.169.151).

\newpage

Attributes managed in the LDAP-Service
--------

The LDAP-Service is based on RFC 2307. The following picture illustrates
the connection between Unix-Objects and the LDAP-Objects.

![LDAP-Objects](img/LDAP-Objects.png)

The attributes of the objects will be filled according the rules
documented in this Concept and will be the same for all LDAP-Clients.

Accounts
--------

Attributes managed for Accounts are the following



Object-Classes
--------



The Account-Object has the following Object-Classes assigned:

- inetorgperson
- organizationalPerson
- person
- posixAccount
- shadowAccount
- extensibleObject
- ldapPublicKey  

**Note:** Only the Attributes documented in this concept will be
managed.

#### Distinguished Name and uid

The uid-attribute is used to create the distinguished name of the
account-objects.

`uid=<unique Unix username>,ou=people,o=global,dc=vfd2,dc=com`

The uid-attribute is taken from the Vodafone D2 LDAP.

#### uidnumber

The uid-numbers from 1000 to 2500 are reserved for technical accounts, like the
HPRI reference users or technical accounts that are used across systems.

The uid-number for accounts which are linked to persons and managed by IdM is
generated uniquely the range of 2500 to 65000.

#### gidNumber

The gidNumber of the primary group is chosen from the organizational
unit of the account-owner and is between 59000 and 59999.

#### homeDirectory

The homeDirectory is `/home/<uid>`.

loginShell
--------

The loginShell is always `/bin/defaultshell`. This shell should be linked
locally to the preferred shell of the workstation.

 userPassword
 --------

T
