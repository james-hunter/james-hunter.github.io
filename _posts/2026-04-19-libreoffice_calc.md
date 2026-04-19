---
layout: post
author: jhunter
title: Making a notes database with LibreOffice calc
categories:
- other
date: 2026-04-19
---

# Sources 

[rijnmond.nl](https://www.rijnmond.nl/luister/podcasts)
[apple.com](https://podcasts.apple.com/nl/search?term=%27t%20opkamertje)
[libreoffice.org](https://ask.libreoffice.org/t/how-to-start-a-program-from-basic-macro/62629)
[microsoft.com](https://devblogs.microsoft.com/scripting/powershell-regex-crash-course-part-4-of-5/)
't Opkamertje spreadsheet notes

# Introduction

For years I have used Apple iTunes on my Windows computers to download and listen to podcasts.
A personal favourite podcast of mine is the variety music podcast from Rotterdam, called "'t Opkamertje" which means "the little room upstairs". 
The show is presented by Roland Vonk and in every episode he presents music from all decades and genres. Sometimes he plays extracts of spoken-word radio programs - political discussion, comedy, and more.
I love the variety of the material. It is a rich source of entertainment that is new and unexpected to me.
There are so many episodes, that I simply can't remember where I heard something. Each audio file that I have only has the program name, the hour and the date.
In addition the radio show came to an in Decmeber 2025.

This motivated me to make a LibreOffice calc speadsheet to order my own notes on the episodes available to me.

# Purpose

My goal was to make the following:
1. A spreadsheet with file data for all the episodes on my computer.
1. A macro in the spreadsheet that will let me play a selected episode.

I planned to have a spreadsheet with these columns:
* Comment (my comments)
* BroadcastDate
* CreationTime
* Name
* FullName
* Hour (the show is sometimes split over two hours, ie over two audio files)

# Begin from where you are

The meta data for each file is only containing this sort of information, and for all of the available files the comments field is empty.

```powershell
PropertyName   PropertyValue
------------   -------------
Name           opkamertje_uur2_30_04_17.mp3.mp3
Size           63.7 MB
Item type      MP3 File
Date modified  2017-04-30 16:50
Date created   2017-04-30 16:49
Date accessed  2026-04-18 17:19
Attributes     A
Perceived type Audio
Owner          Administrators
Kind           Music
Album          Opkamertje
Year           2017
Genre          Podcast
Rating         Unrated
Title          opkamertje_uur2_30_04_17.mp3
Comments       0
Length         00:49:06
Bit rate       ‎181kbps
Protected      No
```

Going by the available data, the episode hour and episode broadcast date will have to be extracted from the file name. I harness regex to get this.

# Parsing data with regex

Over the years the name format varied, so I've had to account for file names that include dates like these examples:
* 2010-11-15_opkamertje_7112010_192k.mp3
* opkamertje_16_12_2012_128k.mp3.mp3
* opkamertje_04_09_22.mp3.mp3

I use the following powershell script to parse the file data. This script was made using pwsh.exe version 7.6.0-preview.4 in vscode.

```powershell
$folderpath1 = "C:\Users\Owner\Music\Podcasts\Opkamertje\"
$files = get-childitem $folderpath1 | sort-object creationtime
$datestring1 = [regex]"((\d{4}\-\d{2}\-\d{2})|(?<=(_| ))\d{1,2}_\d{1,2}_\d{2,4}(?=(\.|\s|_)))"
$hourstring1 = [regex]"(?<=uu(r|))[12]"

$incomplete = @()
$id = Get-Random -Minimum 0 -Maximum 1000
$result = @(foreach($file in $files){
    Write-Progress -Activity "reading files" -Status $file.name -PercentComplete (($files.indexof($file)/$files.count)*100) -id $id

    #date
    $match1 = $datestring1.Matches($file.name)
    $date1 = if($match1.count -ne 0){
        try{
            [datetime]::parseexact(
                $match1.groups[0].value, 'd_M_yy',$null
            )
        }
        catch{
            try{
                [datetime]::parseexact(
                    $match1.groups[0].value, 'dd_M_yyyy',$null
                )
            }
            catch{
                [datetime]::parseexact(
                    $match1.groups[0].value, 'yyyy-M-dd',$null
                )
            }
        };
    }
    else{
        $null
    }

    #hour
    $match2 = $hourstring1.Matches($file.name)
    $hour1 = if($match2.count -ne 0){
        $match2.groups[0].value
    }
    else{
        $null
    }

    if($date1){
        [pscustomobject]@{
            "Comment" = $null
            "BroadcastDate" = $date1
            "CreationTime" = $file.creationtime
            "Name" = $file.name
            "FullName" = $file.fullname
            "Hour" = $hour1
        }
    }
    else{
        $incomplete += $file
    }

    Write-Host "File:`t$($file.name)" -ForegroundColor black
    Write-Host "Date:`t$(get-date($date1) -format "yyyy-MM-dd")" -ForegroundColor black
    Write-Host "Hour:`t$($hour1)" -ForegroundColor black


})
Write-Progress -Activity "reading files" -Completed -id $id

$result | ConvertTo-Csv -Delimiter `t -NoTypeInformation | Set-Clipboard
```

# Creating the spreadsheet

I now open a clean Libreoffice calc workbook with one sheet in it.

I have my file data in my clipboard so I simply paste it into a new Libreoffice calc workbook. I add autofilter to the table, and sort the data by broadcast date. I also format the dates for my preference as "dddd dd MMMM yyyy" for the broadcast date.

In my case, the copy-paste unhelpfully pasted the dates as strings. To convert strings into dates in Libreoffice calc:
1. Select all interesting cells
1. Right-click, and select 'Clear Direct Formatting'
1. Next, do the keyboard shortcut Ctrl+h
1. In the Find field, type: `.+`
1. In the Replace field, type: `$0`
1. In the 'Other Options' area, ensure you tick 'Current Selection Only' and 'Regular Expressions'
1. Click 'Replace All'
1. Now you have numbers which you can format as dates normally.

I add one row at the very top of the sheet, and create a merged row of cells above the table for the worksheet's title. Note that a cell value that begins with an apostrophe requires you write the apostrophe twice.

I also hide the columns 'name' and 'fullname', without deleting them.

The sheet now has hundreds of rows. To improve navigation, I use Libreoffice calc's grouping feature.

Working from top to bottom, I create one empty row before the beginning of each year. In the empty year's comments I write the year name.

To add the grouping, from the top again, I select all the rows for a particular year, then I press F12 on my keyboard. Now the rows for the year are grouped, and I can hide or expand the group with a click. I do this for every year.

![image](https://james-hunter.github.io/pictures/20260419_1.gif)

So I can now add comments to the sheet and keep track of the information on each episode.

# BASIC macro to open audio files

I wish to easily play the episode's audio file from the spreadsheet, so I add a macro into the sheet.

I press Alt + F11.

Under the current workbook ("Untitled1")., I click on New, I accept the module name "Module1" and I am taken to the macro editor.

The cursor is focussed on the macro 'Main' which is in the current workbook's macros: Standard, Module1, Main.

I paste in this code. This code will act on the active worksheet. It will use the row of the actively selected cell to open up the audio file from the fullname column. It will use the default audio player.

```basic
Sub Start_MP3

	Dim oDoc as object
	Dim oSheet as object
	Dim oCurrentselection As Object
	Dim rows1 as integer
	Dim musicpath1 as string
	
	oDoc = ThisComponent
	oSheet = oDoc.getCurrentController().getActiveSheet()
	oCurrentselection = oDoc.CurrentSelection
	rows1 = oCurrentselection.rangeaddress.startrow
	musicpath1 = oSheet.getCellByPosition(4,rows1).string
	oSvc = createUnoService("com.sun.star.system.SystemShellExecute")
  	oSvc.execute(musicpath1, "", 1)
	

End Sub
```

In the editor view, I click save and close the editor view. The name or location of the workbook isn't important.

# Macro button

The macro already works, but I'd like a button to make the macro more accessible.

I press Alt+F11 again.

I navigate to the newly created macro in the current workbook, Standard, Module1, "Existing modules in Module1". I click once on Start_MP3, then at the right I select Assign.

In the next dialogue box, I select the tab Toolbars.

In Categories, I select macros.

Target is Standard.

In the available commands I select my macro Start_MP3, then click the black arrow which points rightwards to add my macro to the standard toolbar.

With my macro selected at the right now, I click on Modify and "Change Icon". I select the icon that looks like a "play" icon.

I click OK, and the dialogue closes.

![image](https://james-hunter.github.io/pictures/20260419_1.gif)

In the top of my workbook I now have a play icon which will run my macro when I click on it.

I save the workbook to complete the configuration.
In the future I will look at this workbook, play audio files from it, and take notes about the audio I hear.

![image](https://james-hunter.github.io/pictures/20260419_2.gif)






