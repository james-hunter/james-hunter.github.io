---
layout: post
author: jhunter
title: using invoke-restmethod
categories:
- powershell
date: 2026-06-14
---

# Sources 

[truesec.com](https://www.truesec.com/hub/blog/invoke-webrequest-or-invoke-restmethod)

[chatgpt.com](https://chatgpt.com)

[stackoverflow.com](https://stackoverflow.com/a/27951845)

[atlassian.com](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/)

`help invoke-restmethod`

`help invoke-webrequest`

# Command (abridged)

`Invoke-RestMethod [-Uri] <System.Uri> [-Body <System.Object>] [-Headers <System.Collections.IDictionary>] [-Method {Default | Get | Head | Post | Put | Delete | Trace | Options | Merge | Patch}]`

# Definition

This command lets me get data from an online resources which are designed for programming, aka "APIs".

Windows powershell also has the commands `invoke-webrequest` and `curl`. The three commands get web data on the command line, but they all three have different purposes.

* `invoke-webrequest` gets web data and returns a rich powershell object. For many situations the data might be too rich.
* `curl` is its own application, so it works differently to the other two commands. Note that `curl` was an alias of `invoke-webrequest` in PowerShell 5.x but not in 7.x.
* `invoke-restmethod` is actually a wrapper for `invoke-webrequest` (see the sources above). Crucially `invoke-restmethod` will convert the data into a handy powershell object. 

All three commands have their uses.

# Options

`-Uri` is followed by the web address of the API.

`-Method` most commonly takes `GET`, `PUT`, or `POST`. `GET` of course lets you read data, but according to an API's documentation, `PUT` or `POST` may be the method to get information. `PUT` and `POST` can be used to make changes to items within the API.

`-Headers` is optional depending on the API, and what you are trying to do. This can contain authentication data. It might be used to signal to the target which kind of data you are sending. It might be used to define what data you are asking for.

`-Body` is where you would put the data that you are sending to the API. A body might be a query statement or it could be variable values that you are trying to update on the API.

There are many other options but these are the ones I use mostly.

# Examples

## Without authentication

This example lets me read the top stories at the CBC. Using this command I get all the info without authentication.

```powershell
invoke-restmethod https://rss.cbc.ca/lineup/topstories.xml

type        : contentpackage
deptid      : 2.630
syndicate   : true
title       : title
link        : https://www.cbc.ca/news/health/cte-diagnosis-research-9.7228387?cmp=rss
description : description
pubDate     : Wed, 10 Jun 2026 05:59:18 EDT
category    :
guid        : guid
...
```

For comparison, if I used this command, I would need to parse the XML found in the `Content` field.

```powershell
Invoke-WebRequest https://rss.cbc.ca/lineup/topstories.xml

StatusCode        : 200
StatusDescription : OK
Content           : <?xml version="1.0" encoding="UTF-8"?>
                    <rss xmlns:cbc="https://www.cbc.ca/rss/cbc" version="2.0">
                       <channel>
                          <title><![CDATA[CBC | Top Stories News]]></title>
                          <description><![CDATA[FOR P…
...
```

## With authentication

The company Atlassian has a product Jira which can be used to track work items. I subscribed to the free version to test out its API.

With my free subscription I get API access to my personal Jira space.

Jira provided me with an APi token, and that's basically my password for the Jira API. I stock the password temporarily in `c:\temp\token.txt`. To use the Jira API I put authentication data into the header of the `invoke-restmethod` command.

```powershell
#Source - https://stackoverflow.com/a/27951845
#Posted by briantist, modified by community. See post 'Timeline' for change history
#Retrieved 2026-05-16, License - CC BY-SA 4.0
$user = "james@contoso.com"
$jira_token = get-content c:\temp\token.txt
$pass = $jira_token
$pair = "$($user):$($pass)"
$encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
$basicAuthValue = "Basic $encodedCreds"
$header = @{
 "Content-Type" = "application/json"
 "Accept" = "application/json"
 "Authorization" = $basicAuthValue
}
```

Now I can send requests to the Jira API. 

```powershell
$myselfuri = "https://contoso.atlassian.net/rest/api/3/myself"
$myselfdata = invoke-restmethod -Uri $myselfuri -Method GET -Headers $header
```

# Final comments

It's great fun to get data from the web. Lots of websites make their resources available over APIs, such as reddit or other news sites.

In a work setting, programming my work with an API and automating it with scheduled tasks is a fantastic time saver.

