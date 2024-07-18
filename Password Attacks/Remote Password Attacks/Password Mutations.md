Use hashcat to mutate a list of words.

Hashcat Rule File Example:
```shell
wannacrye@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

|**Function**|**Description**|
|---|---|
|`:`|Do nothing.|
|`l`|Lowercase all letters.|
|`u`|Uppercase all letters.|
|`c`|Capitalize the first letter and lowercase others.|
|`sXY`|Replace all instances of X with Y.|
|`$!`|Add the exclamation character at the end.|
To make a mutated list:
```shell
wannacrye@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

Generate wordlists:
[CeWL](https://github.com/digininja/CeWL)
```bash
cewl https://example.com -d 4 -m 6 --lowercase -w wordlist.lst
```
- -d: depth to spider
- -m: minimum length of the word
- -w: file to save to