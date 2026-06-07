---
layout: post
author: jhunter
title: For-each loop in bash using find
categories:
- bash
date: 2026-06-07
---

# Sources 

[baeldung.com](https://www.baeldung.com/linux/find-exec-command)

[ubuntuusers.de](https://wiki.ubuntuusers.de/TiMidity/)

# Summary

Coming from the Windows world, when I have an array of things, I want to loop through them with `$array | foreach-object{}` or `foreach($i in $array){}`.

In bash, it's different. You do have `for` but that's not going to work all the time.

I am learning that I have to think of different types of loops for different tasks. In particular, this blog shows how to loop through an array of files.

# `find` command

You can find files with the command `find`. This command has the option `-exec` which lets us execute a command on every found file: here is what I'll use to loop over my array of files.

`find` also has the option `-quit` which makes `-exec` only work upon the first result from `find`.

In the below code, `{}` means 'the found file'. Without going further into details, here is how to get some information about the first file I find:

```bash
find . -name "*.mid" -exec stat {} \; -quit
```

# Personalised functions and `find`

I want to do lots of things with each file. It's handy to write a custom function then run `find` with it.

Here I will use 'timidity' (for midi files) and 'ffmpeg' (for other media). It's key to note that this process can work with other command line applications.

In this example, I begin by creating a function in a text file:

```bash
sudo vi ~/20260607_timidity.sh
#!/bin/bash

function james_invoke_timidity(){
        echo "$1"
        rename=$(echo "$1" | sed s/.mid/.mp3/)
        echo "0 $rename"
        timidity "$1" -Ow -o - | ffmpeg -i - "./mp3/$rename"
}
```

I change the rights of the file so it can be executed.

```bash
sudo chmod +x  ~/20260607_timidity.sh
```

I import the function.

```bash
. ~/20260607_timidity.sh
```

Then export it:

```bash
export -f james_invoke_timidity
```

Now I can run the function with using `bash -c` and therefore in `find`.
 
I run:

```bash
mkdir mp3
find -name "*.mid" -exec bash -c "james_invoke_timidity \"{}\"" \; -quit
```

# Result

After the command completes, I have a subfolder named 'mp3' filled with mp3 files created from the mp3 files in the pwd.

Now I can enjoy the music from those midi files on applications that didn't support midi.




