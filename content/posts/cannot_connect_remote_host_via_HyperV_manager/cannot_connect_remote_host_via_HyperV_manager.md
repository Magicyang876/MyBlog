---
title: "Cannot_connect_remote_host_via_HyperV_manager"
date: 2025-01-07T16:31:41+08:00
draft: false
categories:
    - "tool"
    - "fix"
---
# Table of Contents
<!-- TOC -->

- [Desc](#desc)
- [Fix](#fix)
- [Reference](#reference)

<!-- /TOC -->

## Desc
Somehow cannot connect to remote host via hyper-v manager. 
Error message: 
"the operation on computer failed:the connection to the specified remote host was refused. verify that the ws-management service is running on the remote host and configured to listen for requests on the correct port and http url"

- Network is ok, remote desktop can connect.
- WS management service is running.
- Firewall is not blocked.
- Remote host has been add to the trusted list on client.
- <Test-WSMan -ComputerName "RemoteHostName"> failed with same error message.

## Fix
Proxy issue!!!
I have set the proxy for powershell via the below cmd.

    netsh winhttp show proxy
But I forgot to unset it. So, what you need to do is unset the proxy via below cmd and everything will be ok.

    netsh winhttp reset proxy


## Reference
https://qianxu.run/2021/08/18/powershell-proxy/index.html