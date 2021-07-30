
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

---------------------------------------------------------------------------------------
Attribute              Posix  Description
--------------------- ------- ---------------------------------------------------------
uid                   X       unique UNIX username (created by D2 LDAP, e.g.: amuelle0)

givenName                     given name

sn                            surname

cn                    X       full name (displayName of the D2 LDAP, e.g. "Müller,
                              Adalbert, VF-Germany")

mail                          email address (e.g. adalbert.mueller@vodafone.com)

othermailbox                  email address for OSS Mailserver

mobile                        mobilenr., if available

homePhone                     fixed line telephone nr., if available

employeeNumber                German employee nr. if available

ou                            short name of the department (e.g. TOOO)

departmentNumber              HRPI of the user (7-character code for the Team the user
                              belongs to)

l                             regional location of the User(e.g. N,NW,O,...)

uidnumber             X       Unix uidNumber, as defined by TJVI-Rules

gidNumber             X       primary Group-ID of the account

homeDirectory         X       Unix home directory, e.g. /home/amuelle0

loginShell            X       Unix login shell, e.g. /bin/bash

userPassword                  hashed password of the user

sshPublicKey                  optional SSH public key which may be used by systems to
                              authenticate users without a password
---------------------------------------------------------------------------------------

#### Object-Classes

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

#### loginShell

The loginShell is always `/bin/defaultshell`. This shell should be linked
locally to the preferred shell of the workstation.

#### userPassword

The Password has the following Format:

---------------------------
Attribute           Value
------------------- -------
Minimum Length      8

Minimum Lowercase   1

Minimum Numeric     2

Minimum Uppercase   1

Minimum Special     2
---------------------------

The policy is controlled by the LDAP-Server. Passwords have to be
written unhashed to the Server.

#### sshPublicKey

This multiple attribute stores an optional SSH public key. The content has to be
formatted identically to a line in the authorized_keys.

### Posix-Groups

Posix-Groups are mainly based on location, department and teams.

---------------------------------------------------------------------
Attribute   Description
----------- ---------------------------------------------------------
cn          Name of the group

gidnumber   Unix gidNumber

memberUid   multiple attribute containing the uid's of the accounts
---------------------------------------------------------------------


#### Object-Classes

The Group-Object has the following Object-Classes assigned:

-   posixGroup

#### Distinguished Name and cn

The cn-attribute is used to create the distinguished name of the
group-objects.

`cn=<group-name>,ou=group,o=global,dc=vfd2,dc=com`

#### gidNumber

The gidNumbers are assigned by the LDAP-Admins during the creation of
the group.

gidNumbers between 59000 and 59999 are reserved for organizational
groups.

The LDAP will assign gidNumbers in the Range from 2500 to 37499 for
specific groups.

### Netgroups

The Netgroups can be used to control the access to specific service
platforms.

-----------------------------------------------------------------------------
Attribute           Description
------------------- ---------------------------------------------------------
cn                  Name of the netgroup

nisNetGroupTriple   multiple attribute containing the uid's of the accounts
-----------------------------------------------------------------------------

The netgroup `allusers` can be used to deny or allow access to all users
defined in the LDAP.

#### Object-Classes

The netgroup-Object has the following Object-Classes assigned:

-   nisNetgroup

#### Distinguished Name and cn

The cn-attribute is used to create the distinguished name of the
group-objects.

`cn=<netgroup-name>,ou=netgroup,o=global,dc=vfd2,dc=com`

#### nisNetGroupTriple

The nisNetGroupTriple may contain several values including the users
uid. The general format is `(host,user,domain)` (including the
brackets). In this environment only the user is supported. Therefor the
format for this environment is `(,user,)`. The user-field contains the
uid of the account-object that should be included in this netgroup.

A sample would be `(,amuelle0,)`.

### LDAP-Groups

LDAP-Groups can be created to manage the authorization for non-Unix Applications.

-----------------------------------------------------------------------------
Attribute   Description
----------- -----------------------------------------------------------------
cn          Name of the group

member      multiple attribute containing the cn of the accounts
-----------------------------------------------------------------------------

#### Object-Classes

The Group-Object has the following Object-Classes assigned:

-   groupOfNames

#### Distinguished Name and cn

The cn-attribute is used to create the distinguished name of the
group-objects.

`cn=<group-name>,ou=ldapgroup,o=global,dc=vfd2,dc=de`

### Mail-Groups

Mail-Groups can be created to manage the mailing lists for the OSS Mailserver.

-----------------------------------------------------------------------------
Attribute             Description
--------------------- -------------------------------------------------------
cn                    Name of the group

mail                  email address of the mailing list

mgrpRFC822MailMember  multiple attribute containing the email address of the
                      members
-----------------------------------------------------------------------------

#### Object-Classes

The Group-Object has the following Object-Classes assigned:

-   mailGroup

#### Distinguished Name and cn

The cn-attribute is used to create the distinguished name of the
group-objects.

`cn=<group-name>,ou=mailgroup,o=global,dc=vfd2,dc=de`

## Security-Concept

### General Access to Data

The User Repository stores various personal attributes of the people that
are using this solution to authenticate against several systems. The
goal of the Security-Concept is to ease the use of the service on the
one hand and to restrict the access to private data of the people
involved.

The following picture outlines the access to different parts of the
person-object.

![LDAP-Security](img/LDAP-Security.png)

The posixAccount-Attributes are considered public and can be accessed
without authentication. This enables the usage of the service by several
Unix Operating-Systems with creating proxy-users for every system.

The person-, inetOrgPerson- and organizationalPerson-Attributes are
considered confidential and can only be accessed by authenticated and
authorized users.

The user may change is own password using LDAP. This enables the user to
use the passwd-command on most LDAP-Clients (see also [LDAP Clients
using Password
Policies](LDAP Clients using Password Policies)).

### Control Access to Service-Platforms

The access to Service-Platforms can be controlled using netgroups.
There will be several netgroups prepared that can be configured locally
in the LDAP-Clients. The procedures are described in [LDAP Clients](../LDAP Clients)
for the different Unix-Operating-Systems.

### Encryption

The network-traffic to the LDAP-Service should be encrypted using SSL.
The servers are configured to accept sessions using LDAP with StartTLS
or LDAPS.

The passwords are hashed in the Directory using SSHA-Schema of the Sun
Directory Server.
