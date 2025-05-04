---
layout: post
author: jhunter
title: LibreOffice Recent Files
categories:
- powershell
date: 2025-05-04
---

# Source

[copilot.microsoft.com](https://copilot.microsoft.com)

[red-gate.com](https://www.red-gate.com/simple-talk/sysadmin/powershell/powershell-data-basics-xml/)

[w3schools.com](https://www.w3schools.com/xml/)

# Summary
When I see data in my computer that I can't access, I'm always curious about how I can get that data into a powershell object.

Today my interest was grabbed by the recent files list in LibreOffice.

I have developed a function that lists the items from the recent documents display.

# XML and namespaces
The `select-xml` command can be difficult to work with because XML data often uses namespaces.

The first five lines of the function use code which can be re-used with other XML files.

# Tip
Open XML files in your browser to get insight into how the data looks.

# Function
```
Function James-Get-LORecent{
    

	[xml]$info = Get-Content "$env:APPDATA\LibreOffice\4\user\registrymodifications.xcu" 
	$name = [string]($info.items.Name -split(":"))[0]
	$uri = [string]($info.items.NamespaceURI)
	$expression1 = "@{" + $name + "='" + $uri + "'}"
	$ns1 = invoke-expression -command $expression1
    
    
	#display the history in Libreoffice, in order
	$xpath1 = "//item[contains(@$($ns1.Keys):path,'ItemList')]"
	$result1 = foreach($item in (Select-Xml -Xml $info -Namespace $ns1 -XPath $xpath1).node.node){
		$xpath2 = "//item[contains(@$($ns1.Keys):path,'OrderList')][./node/prop/value='$($item.name)']" 
		$order1 = (Select-Xml -Xml $info -Namespace $ns1 -XPath $xpath2).node.node.name
	 	$name1 = ($item.prop | Where-Object{
			$_.name -eq "Title"
	       	}).value
    	
		[pscustomobject]@{
	        	"order" = [int]$order1
	        	"name" = $name1
		        "fullname" = $item.name
	       }
	}
    
	return $result1 | Sort-Object order | Select-Object name,fullname
}

```

# Example

The funtion is very easy to use.

Post the script above, and paste it into PowerShell.

To run the function, enter the function name:
```powershell
James-Get-LORecent
```
