---
layout: post
title: Fix, VSTS agent failed in getBuild with error Error self signed certificate in certificate chain
comments: true
tags: [Microsoft, Azure, Visual Studio Team Services, VSTS, VSTS Agent]
description: Fix for, VSTS agent failed in getBuild with error Error self signed certificate in certificate chain
---

If you are getting error like this, good news you are reading right blog article 😄

```
The automated CD deployment is failing on the Release since this morning. It was working fine earlier.
The error is:
2018-05-15T19:16:29.5467063Z ==============================================================================
2018-05-15T19:16:29.5467201Z Task : Download Build Artifacts
2018-05-15T19:16:29.5467283Z Description : Download Build Artifacts
2018-05-15T19:16:29.5467360Z Version : 0.133.4
2018-05-15T19:16:29.5467468Z Author : Microsoft Corporation
2018-05-15T19:16:29.5467540Z Help :
2018-05-15T19:16:29.5467637Z ==============================================================================
2018-05-15T19:16:30.5523744Z Error : in getBuild, so retrying => retries pending : 4
2018-05-15T19:16:34.2060897Z Error : in getBuild, so retrying => retries pending : 3
2018-05-15T19:16:38.2212775Z Error : in getBuild, so retrying => retries pending : 2
2018-05-15T19:16:42.2213719Z Error : in getBuild, so retrying => retries pending : 1
2018-05-15T19:16:46.2409057Z ##[error]Failed in getBuild with error: Error: self signed certificate in certificate chain
2018-05-15T19:16:46.2595096Z ##[error]Error: self signed certificate in certificate chain
```

1. To remove the agent:
c:/vstsagent/a1> .\config.cmd remove

2. Then get your corp SSL certificate(.pem file) and put it in the root folder of the agent i.e. c:\vsts\a1 in my case. To get the .pem file:

Export CA cert from Trusted Root CA Store, use Base64 Encoding X.509 (.CER) format, name the export cert to something like ca.pem.

**Your ca.pem might looks like following:**
-----BEGIN CERTIFICATE-----
(Your Root CA certificate: ca.pem)
-----END CERTIFICATE-----

3. Run the config command again (to configure the agent again with the sslcacert param and anything else you need)
.\config.cmd --sslcacert ca.pem --deploymentgroup --deploymentgroupname "deployment-group-name" --agent $env:COMPUTERNAME --runasservice --work '_work' --url 'https://yourcorpname.visualstudio.com/' --projectname 'your-project-name' --auth PAT --token YOURPATTOKENHERE;

and that's all error fixed.