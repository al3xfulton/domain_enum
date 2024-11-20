# Domain Enumeration

## Desired Learning Objectives
- Demonstrate the use of LDAP Domain Dump to retrieve information about a domain.
- Demonstrat the use of LDAP Searches to query and retrieve information about a domain.
- Describe the role of a Domain in a Windows Environment.
- Describe the role of LDAP in a Windows Domain.
- Describe the relationship between LDAP and Active Directory.
- Describe an Active Directory and it's key terms.


## Domain Basics

### Domain
A domain is a structure which organizes network resources and users allowing for application of policy, authentication and sharing of data and resources.

### Active Directory
Active Directory (AD) is the standard windows directory service implementation which enables:
- Authentication
- Group and User Management
- Policy Adminstration 

Framework drives toward different levels of objects:
- Domain
- Tree
- Fores

![Domain Heirarchy](https://www.cayosoft.com/wp-content/uploads/2022/09/Active-Directory-Forest_blog.jpg)

Organizational Units (OUs)
- The smallest organizational unit within AD
- Allow for heirarchy to seperate by different criteria
- Allow for application of specific group policy settings 
- Establish trust relationships between different OUs


### Domain Controller
Domain Controllers (DCs) are the servers which implements the Active Directory services in a domain.

A DC implements:
- Authenticate users and validate their access permissions accross domain joined devices
- Tracks resources in the network (ie file shares and printers)
- Push Group Policy to all network connected devices and/or users 
- Can runadditional network responsibilities
  - DNS
  - DHCP

## LDAP (Lightweight Directory Access Protocol)
LDAP is a standard protocol that allows applications to interact with Directory Services.

Standard port is over 389

Query to identify details within the directory service by any domain user.

LDAP is used within AD to have a standard way to communicate
- Other directory services like OpenLDAP also are possible

## Kerberos
More secure authentication mechanism that is used to authenticate both the user and service via a Key Distribution Center (KDC) which is often a resource given by the DC.

First implemented in Windows 2000 - replaced NTLM which shifted from Server direct authentication to Third 
Party (DC/KDC) authentication.

Required port is 88 udp + tcp - other ports can be used for non-essential functions: 749/tcp, 4444/udp/464/udp

The KDC on allows for users to maintain Ticket Granting Tickets (TGT) during a standard session. When a user wants to access a service it sends a TGT along with the service name, which is then encryped using the Ticket Granting Service (TGS) secret key. The client then sends that session key to the server to allow for authenticaiton.

![Kerberos Authentication](https://info.varonis.com/hubfs/Imported_Blog_Media/Kerberos-Graphics-1-v2-787x790.jpg?hsLang=en&_gl=1*34btol*_gcl_au*MTQyNzcwNjE3Ny4xNzMyMTI4MzM3)

## Enumeration

Start with what you already have:
- DNS Records
- Established connections
- Identify the Domain Controller


User Enumeration:
```
net user
net user /domain
net user [username]
net user [username] /domain
wmic useraccount
```

```
ldapsearch -H ldap://test.local -b DC=test,DC=local "(objectclass=user)"
ldapsearch -H ldap://test.local -b DC=test,DC=local "(&(objectclass=user)(name=[username]))"
```

Computer Enumeration:
```
net group "Domain Computers" /domain
net group "Domain Controllers" /domain
```

```
ldapsearch -H ldap://test.local -b DC=test,DC=local "(objectclass=computer)"
ldapsearch -H ldap://test.local -b DC=test,DC=local "(&(objectclass=computer)(name=[computername]))"
```

Group Enumeration:
```    
net localgroup
net group /domain
net localgroup [groupname]
net group [groupname] /domain
wmic group
```
```
ldapsearch -H ldap://test.local -b DC=test,DC=local "(objectclass=group)"
ldapsearch -H ldap://test.local -b DC=test,DC=local "(&(objectclass=group)(name=[groupname]))"
ldapsearch -H ldap://test.local -b DC=test,DC=local "(&(objectclass=group)(name=*admin*))"
```

Domain Information:
```
wmic ntdomain
ipconfig /all
```
```
ldapsearch -H ldap://test.local -b DC=test,DC=local "(objectclass=trusteddomain)"
```
DNS Information:

```
Do some neat LDAPSearch for DNSRecords here
adidnsdump
```
## Dump Data
Alternatively we can query to get everything that we have credentials to ask for all the information that user is allowed to query.

`ldapdomaindump`

This allows for local search of all the data which is then represented locally.

Additional potential other ways to view this:

bloodhound - see pretty images representing the hierarchy of domain data

## References
https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/using-the-organizational-domain-forest-model

https://www.varonis.com/blog/active-directory-forest

https://www.serveracademy.com/blog/active-directory-101-a-step-by-step-tutorial-for-beginners/

https://blog.netwrix.com/active-directory-basics/

https://www.okta.com/identity-101/what-is-ldap/#:~:text=LDAP%20is%20a%20protocol%20that,work%20together%20to%20help%20users

https://ldap.com/basic-ldap-concepts/

https://ldap.com/the-ldap-search-operation/

https://www.varonis.com/blog/the-difference-between-active-directory-and-ldap

https://www.varonis.com/blog/domain-controller

https://www.cayosoft.com/what-is-an-active-directory-forest/

https://www.lepide.com/blog/understanding-active-directory-ous-vs-groups/

https://gist.github.com/its-a-feature/1a34f597fb30985a2742bb16116e74e0

https://notes.benheater.com/books/active-directory/page/ldapdomaindump

https://notes.benheater.com/books/active-directory/page/ldapsearch

https://www.oreilly.com/library/view/kerberos-the-definitive/0596004036/ch06s05s01.html
