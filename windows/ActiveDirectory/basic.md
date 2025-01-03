* centralizes management by organizing resources into hierarchical structures
* ADFS -> way to store that data 
* Original idea was X.500
* 2003 -> introduced forest (containers)
* 2008 -> ADFS (Single Sign on)
    * claims based access control
    * introduced recycle bin (R2)
        * recovery of deleted objects
        * default is 60 days; attributes perserved
* 2016 -> migrate to cloud/enhanced security such as GMSA (secure way of running automated tasks)


# Terms
* sysvol - public files in domain (such as system policies, startup scripts, etc.). 
    * replicated to all DCs within the env using File Replication Services (FRS)
* Msbrowse 
    * like a list of all resources (legacy). Can be biewed with `nbtstat -A [target]` or `nltest`
* AD Recycle Bin
    * Deleted objects go here; Great thing is it perserves the attributes
* Tombstone
    * If no recycle bin, objects go here (can range from 60 t0 180 days); no attributes are preserved
* NTDS.Dit
    * the thing that holes your AD data 
        * user/group
        * group membership / password hashes
    * `store password with reversible encryptino`, then cleartext is stored.
* sIDHistory
    * all the things you were previously assigned; meant to help with migrations
    * if sidFiltering not enabled, can be used to eelevate access
* Replication
    * dnoe via the Knowledge Consistency Checker Service
* Global catalog
    * contains copies of ALL objects in the forest
* sAMAccountName (20 chars of less; unique)
* Flexible Single Master Operation Roles
    * five roles
        * Schema Master (one each forest)
        * Dmoain Naming Master (one of each per forest)
        * Relative ID (RID) Master (one per domain)
        * Primary Domain Controller (PDC) emulator (one per domain)
        * Infrastructure Master (one per domain)
    * all five are assigned to firest root
    * when new domain added -> RID Master, PDC Emulator, Infra Master are assigned
* Foreign Security Principals
    * belongs to trusted external forest
    * holds SID of the foreng object
        `cn=ForeignSecurityPrincipals,dc=test,dc=local`

## Trusts
* root domains trust child domains, but child domains in a forest A will not necesssarily have trusts with child domains in B (i.e. the top levels will trust each other)
* All domains in a tree share a standard Global Catalog which contains all information about objects that belong to the tree.
* Trees -> begin at single root domain
* Types
    - parent-child - domains within same forest.
    - cross-link - trust between child domains
    - external : non transitive trust between two different domains (i.e. different forests); SID Filtering
    - tree-root : 2 way between forest root and new tree root domain
    - forest : between two forest root domains

## AD Functionality
* Schema Master - read/write copy of the AD Schema (attributes for an obj)
* Domain Naming Master - domain names/checks that there arent existing
* Relative ID (RID) Master - assigns block of rids to other dcs; ensures the same sid isnt deployed
* PDC Emulator - manage gpos/authentication requests/password changes. manages time as well
* Infrastructure Master - translate guids/sids/dns between domains
    - if this is broken, you'll see SIDs instead of fully resolved names


## Functional Levels
TODO - `https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754918(v=ws.10)?redirectedfrom=MSDN`


## Kerberos
- password converted to NTLM hash (KRB_AS_REQ) (Request TGT)
- DC checks AS-REQ, responds with TGT (KRB_AS_REP)
- User presents TGT to request TGS (KRB_TGS_REQ)
- Server encrypts TGS with NTLM of the target service (KRB_TGS_REP)


## DNS
* DC maintains DNS records via service records (SRV)
    - allows you to find file server, printer, DC, etc
* Dynamic DNS to make changes
* Client locates DC by sending query to the dns service
    - recieves srv record from the dns database.
## RPC
* interprocess client/server remote procedure calls (MSRPC)
* AD uses 4
    - lsarpc
        - Local Security Authority (loacl sec policy, audit policy, interactive auth, domain sec policies)
    - Netlogon
        - authenticate users/services in domain
    - Remote SAM (samr)
        - management of domain account database (users/groups). 
        - Example: Bloodhound 
    - drsuapi
        - Directory Replication Service (DRS) Remote Protocol
        - this is how NTDS.dit copying can be done


## Local Accounts
* Administrator - Windows 10 and Server 2016 hosts disable the built-in administrator account by default   
    - it creates another local account in the local administrator's group during setup.
* Network Service
    - predefined local account used by the Service Control Manager (SCM) 
    - When a service runs in the context of this particular account, it will present credentials to remote services.

* Local Service
    - predefined local account used by the Service Control Manager (SCM) for running Windows services. 
    - configured with minimal privileges on the computer and presents anonymous credentials to the network.

## Groups/OUS
* Groups are primarily used to assign permissions to access resources. OUs can also be used to delegate administrative tasks to a user, such as resetting passwords or unlocking user accounts without giving them additional admin rights that they may inherit through group membership.

* distribution vs security group (former is like email, latter is for permissions)
* Scopes
    - local
        - Domain local groups can only be used to manage permissions to domain resources in the domain where it was created. Local groups cannot be used in other domains but CAN contain users from OTHER domains. Local groups can be nested into (contained within) other local groups but NOT within global groups.
    - domain
        - Global groups can be used to grant access to resources in another domain. A global group can only contain accounts from the domain where it was created. Global groups can be added to both other global groups and local groups.
    - global
        - The universal group scope can be used to manage resources distributed across multiple domains and can be given permissions to any object within the same forest. They are available to all domains within an organization and can contain users from any domain. Unlike domain local and global groups, universal groups are stored in the Global Catalog (GC), and adding or removing objects from a universal group triggers forest-wide replication