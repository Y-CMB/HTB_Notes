***POST FOOTHOLD***

Key Terms to Search For:
- Passwords
- Username
- Users
- configuration
- pwd
- Passphrases
- User account
- Passkeys
- dbcredential
- Login
- Keys
- Creds
- dbpassword
- Credentials

## How to Search
- Search bar
- [Lazagne](https://github.com/AlessandroZ/LaZagne) - third-party tool for discovering unsecure credentials
- [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr) - cmd

### Lazagne
```cmd
start lazagne.exe all -v
```

*or (real testing worked with)*

```cmd
cmd /k LaZagne.exe all -v
```

### findstr
```cmd
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

## Important Places to Look
- Passwords in Group Policy in the SYSVOL share
- Passwords in scripts in the SYSVOL share
- Password in scripts on IT shares
- Passwords in web.config files on dev machines and IT shares
- unattend.xml
- Passwords in the AD user or computer description fields
- KeePass databases --> pull hash, crack and get loads of access.
- Found on user systems and shares
- Files such as pass.txt, passwords.docx, passwords.xlsx found on user systems, shares, [Sharepoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration)

