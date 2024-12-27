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