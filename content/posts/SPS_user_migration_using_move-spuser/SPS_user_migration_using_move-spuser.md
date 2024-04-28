---
title: "SPS_user_migration_using_move-spuser"
date: 2024-04-28T09:55:45+08:00
draft: false
categories:
    - "sps"
---

## Desc
### 有时，我们需要迁移用户域或在多个身份验证提供者之间迁移并保留权限。
### Move-SPUser命令的使用场景
#### a. 用户账户在AD中被删除并重新创建（带有新的SID）。
#### b. 用户账户从一个域转移到另一个域。
#### c. 用户账户的登录ID发生变更（如姓氏变更,认证变化）。

## Steps
#### 1. 使用STSADM工具迁移用户（SharePoint 2007及以前版本）：
    Stsadm -o migrateuser -oldlogin domain\OldUserID -newlogin domain\NewUserID -ignoresidhistory
#### 2. SharePoint 2013及以后版本使用Move-SPUser PowerShell命令：
    $WebURL="https://intranet.contoso.com"
    $Web = Get-SPWeb $WebURL
    $OldID="i:0#.w|contoso\Account"
    $NewID="i:0#.w|contoso_new\Account"
    $OldUser = $Web.EnsureUser($OldID)
    Move-SPUser -Identity $OldUser -NewAlias $NewID -ignoresid -Confirm:$false
#### 3. 也可以迁移同一域内的账户：
    $user = Get-SPUser -Identity "i:0#.w|DOMAIN\aaa" -Web https://webUrl
    Move-SPUser -Identity $user -NewAlias "Domain\aaa_2" -IgnoreSid
#### 4. 批量操作
##### 准备一个CSV文件，映射旧域用户到新域用户。使用PowerShell脚本循环处理CSV文件中的每个用户，执行Move-SPUser命令。

#### 5. 迁移用户配置文件常用方法：
    Add-PSSnapin Microsoft.SharePoint.PowerShell
    $SiteURL = "https://intranet.contoso.com/sites/xxx"
    $oldDomain = "contoso"
    $newDomain = "contoso_new"
    $Users = Get-SPUser -web $SiteURL
    foreach ($oldUser in $users){
        $OldUserID = $oldUser.userlogin
        $newUser = $OldUserID.replace($oldDomain, $newDomain)
        move-SPUser -Identity $oldUser -NewAlias $newUser -IgnoreSID -confirm:$false
    }

#### 6. 迁移AD组
#### 使用PowerShell脚本迁移活动目录安全组：
    $OldLogin="DomainA\Group"
    $NewLogin="DomainB\Group"
    $Farm = Get-SPFarm
    $Farm.MigrateGroup($OldLogin, $NewLogin)
