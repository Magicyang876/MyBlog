---
title: "SPS_block_aspx_file_type"
date: 2024-07-03T11:06:03+08:00
draft: false
categories:
    - "sps"
---
# Table of Contents
- [Background](#background)
- [Desc](#desc)
- [Workaround](#workaround)
  - [在Web应用程序级别控制活动内容](#在web应用程序级别控制活动内容)
- [其他建议-使用域隔离来控制活动内容](#其他建议-使用域隔离来控制活动内容)
- [总结](#总结)


## Background
活动内容是指在用户浏览器中执行并代表用户执行操作的网络标记或控件。JavaScript 是最常见的活动内容类型。

高权限用户可以在 SharePoint 站点中嵌入活动内容。恶意用户可以添加活动内容，与其他 SharePoint 页面进行交互，执行未经受害者同意的不良操作。
- 读取、贡献和编辑权限级别不包括添加和自定义页面权限级别。这些用户无法添加或修改活动内容。

- 设计和完全控制权限级别包括添加和自定义页面权限。这些用户可以添加或修改活动内容。

- 网站集合管理员隐式授予添加和自定义页面权限。

## Desc
SharePoint管理中心的“阻止的文件类型”设置默认不限制ASPX文件的上传下载，因此不生效是预期行为。

## Workaround
SharePoint提供对主动上传的ASPX文件禁用加载的方法，也就是禁用custom scripts。可以使用管理员身份运行下面的PowerShell指令，对单个站点关闭custom scripts：

    (Get-SPSite -Identity <siteURL>).DenyPermissionsMask = [Microsoft.SharePoint.SPBasePermissions]::AddAndCustomizePages

如需重新启用custom scripts

    (Get-SPSite -Identity <siteURL>).DenyPermissionsMask = [Microsoft.SharePoint.SPBasePermissions]::EmptyMask
如需验证当前状态，可以运行：

    (Get-SPSite -Identity <siteURL>).DenyPermissionsMask
默认返回值是EmptyMask，表示启用。

### 在Web应用程序级别控制活动内容
  SharePoint管理员可能希望限制整个Web应用程序中的活动内容使用。例如，每个用户都是自己的My网站上的站点集管理员，对于SharePoint管理员每次提供新的My网站都手动设置DenyPermissionsMask是不切实际的。
  Web应用程序策略可用于拒绝特定Web应用程序中所有用户的添加和自定义页面权限。

    $w = Get-SPWebApplication http://contoso-my
    $p = $w.PolicyRoles.Add("NoActiveContent", "Denies active content")
    $p.DenyRightsMask = [Microsoft.SharePoint.SPBasePermissions]::AddAndCustomizePages
    $w.Update()

## 其他建议-使用域隔离来控制活动内容
一些 SharePoint 部署被配置为允许“我的网站”或“自助站点创建”，这使用户可以创建一个站点集，他们被授予站点集管理员权限。

如果您选择允许用户在这些站点中嵌入活动内容，请考虑将其托管在一个使用与其他受信任内容不同域名的 Web 应用程序上。同源策略将阻止这些站点上的不受信任活动内容与其他域上的受信任内容进行交互。

例如，受信任内容可能托管在 http://contoso.com 上。考虑在 http://contoso-my.com 上的一个独立的 Web 应用程序上启用“我的网站”和“自助站点创建”。

## 总结
管理员在SharePoint Server中有四种控制活动内容的方法：

- 给予适当的权限级别，防止用户处理活动内容

- 在站点集中禁用添加和定制页面权限

- 在网站应用程序中禁用添加和定制页面权限

- 将活动内容隔离到单独的域中

这些技术允许SharePoint管理员减轻不受信任的活动内容的影响。