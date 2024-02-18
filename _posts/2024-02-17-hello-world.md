---
title: Hello World
date: 2024-02-17 15:22:10 -0600
categories: [hello world]
tags: [hello world]
---

# Hello World

Hello world, this is my first test blog post.   I'm using this post to see how to post some PowerShell code.

### PowerShell

```powershell
Write-Host -ForegroundColor Green "Input the user name in question: " -NoNewline

$User = Read-Host 

Get-ADUser -identity $User -Properties msDS-UserPasswordExpiryTimeComputed | 
    select samaccountname,@{ Name = "Expiration Date"; Expression={[datetime]::FromFileTime($_."msDS-UserPasswordExpiryTimeComputed")}}
```