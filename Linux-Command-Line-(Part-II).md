# Basics of the Command Line
modified from [this](https://github.com/onyxfish/command-line-tutorial/blob/master/README.md) tutorial.

This tutorial will give a brief introduction to the Unix *(Unix is an umbrella for Linux/macOS/android and other systems)* command line. For purposes of this introduction **command line**, **shell**, **terminal** and **bash** are all the same thing. There are specific, technical meanings for each of these words, but for now they don't really matter.

The main takeaways is becoming familiar with the command line, folder/file pathing, I/O redirection, and how to run programs. This is by no means an exhaustive tutorial, there are a lot of nuances and much, much customization and scripts that can automate a lot of things, but for now we will keep it simple.

**[You should open up this cheat sheet now](files/cheat-sheet.pdf)** as it has the commands will be covering in a quick one page reference sheet.

**I also highly recommend** doing this [quick course](https://www.codecademy.com/learn/learn-the-command-line) from codecademy that gives a more in depth dive into the Unix command line.

Alright, lets jump into it!

Logging into newton
--------------------------

We are going to run everything from Newton so we are working in the same environment. Please log in now, if you haven't setup Newton yet, please follow the [Newton-Cluster-Setup-(Part-I)](Newton-Cluster-Setup-(Part-I).md) tutorial to get setup.

Connect to the UCF VPN. Then, open a PowerShell (Windows 10) or Terminal (Linux/macOS) prompt now, and type:

```bash
ssh newton
```

You should get a welcome message and be logged into Newton now.

Running your first command
--------------------------
The dollar sign `$` represents your `prompt`, everyone's will be slightly different, but usually will contain your username you are currently logged in under, followed by the `@` symbol and some letters/numbers. After the dollar sign will be what you want to input as a command.

Now, lets run the first command:

```bash
$ echo hello
hello
```

``echo`` just outputs what you tell it the command line, basically a print function. Very useful for quickly appending files from the command line or for use in scripts.

# File/Folder Commands

Where am I?
-----------

The command line has a sense of *location*, that is, what folder you are *in* at any given time. This is called your **current working directory**. (**Directory** is just another name for a "folder".) To see what directory you are in right now:

```
$ pwd
/home/username
```

`pwd` is short for "print working directory". By default the terminal always starts you in your **home directory**, which is named with your login **username**. This is where most of your configuration files for your user and files are stored.

What's here?
------------

To see what files are inside the **current working directory**, use the `ls` command:

```bash
$ ls
Applications			bin
Confidential			confidential.py
Desktop				    confidential2.tc
Documents			    external_lbrs
Downloads			    feed.json
Dropbox				    feed.py
Library				    floobits
Movies				    gitconfig
Music				    google_analytics_auth.dat
Pictures			    pgadmin.log
Public				    s3cfg
SpiderOak Hive			src
VirtualBox VMs			syria
adt-bundle-mac			tmp
android-sdk-macosx
```

What's in there?
----------------

What if you want to see what files are in a different directory?

```
$ ls Documents
Arduino				android-workspace
Aspyr				eagle
GoPro Projects		gopromote.prm
Hero Lab			nicar_expenses.pdf
Klei				nicar_receipts.pdf
Library				orchard bank travel number.txt
Logan.game			pedagogy.txt
Logans.band			piano 1.band
MapBox				test.txt
NACIS notes.txt		tyler-env.txt
Roblox
```

By passing `Docuemnts` as an **argument** to `ls`, it's shown me the files in my `Documents` directory, which is inside my **home directory**.

Some things are hidden
----------------------

By default, the `ls` will not show you files whose names being with a **period**. These are called **hidden files** are are usually related to configuring programs. However, it's very often useful to see these files, which you can do with the `-a` flag:

```bash
$ ls -a
.				        .pypirc
..				        .python-eggs
.CFUserTextEncoding		.qgis
.DS_Store			    .qgis2
.MacOSX			    	.relay.conf
.NERDTreeBookmarks		.rnd
.Platformer		       	.rstudio-desktop
.Rhistory			    .s3cfg
.Rube Goldberg			.spyder2
.Trash			    	.sqlite_history
.Xauthority		    	.ssh
.android		    	.subversion
.anyconnect		    	.teamocil
.bash_history			.tilemill
.bash_profile			.tmux.conf
.build			    	.tox
.cache			    	.uibtedbn
.clan_auth.dat			.vagrant.d
.clan_secrets.json		.vim
.config			    	.viminfo
.cordova		    	.viminfo.tmp
.cups			    	.viminfz.tmp
.distlib		    	.vimrc
.dropbox		    	.virtualenvs
.dropbox-master			.wireshark
.eaglerc		    	.wireshark-etc
.ec2			    	.ypp_42
.floorc		    		.zcompdump
.fontconfig		    	.zcompdump-nomad-5.0.2
.gem			    	.zprofile
.gitconfig		    	.zsh-update
.gitsh_history			.zsh_history
.gnome2			    	.zshrc
.gnupg			    	Applications
.goaccessrc		    	Confidential
.godot			    	Desktop
.haxelib		    	Documents
.heroku			    	Downloads
.hkzftsorc		    	Dropbox
.hxcpp_config.xml		Library
.hxcpp_config.xml.bak	Movies
.ievms			      	Music
.infinit		    	Pictures
.inkscape-etc			Public
.ipython		    	SpiderOak Hive
.keybase		    	VirtualBox VMs
.keybase-installer		adt-bundle-mac
.lesshst		    	android-sdk-macosx
.lighttable		    	bin
.local			    	confidential.py
.matplotlib		    	confidential2.tc
.mongorc.js		    	external_lbrs
.netrc			    	feed.json
.ngrok			    	feed.py
.node-gyp		    	floobits
.npm			    	gitconfig
.oh-my-zsh		    	google_analytics_auth.dat
.pgadmin_histoqueries	pgadmin.log
.pgpass			    	s3cfg
.pip			    	src
.psql_history			syria
.pylint.d		    	tmp
```

You saw this briefly when you needed to modify your config file in your `.ssh` directory.

Now, what if we want to know how much storage each directory/file has and the permissions on those files? You can use

```bash
$ ls -asl
```

Permissions is out of scope for this tutorial, but this provides a clean list of viewing the most information about all the files in a directory.

Navigating paths
-------------

In order to be effective on the command-line, you need to understand **paths**. A path is a series of **directory** names, separated by ``/`` and sometimes ending with a filename. A **directory** is just another name for a folder. Let's go back to `pwd`:

```bash
$ pwd
/home/username
```

So ``/home/username`` is the **path** to my **home directory**.

What if wanted the path to the `src` directory that is inside my home directory. Then it would be:

```
/home/username/src
```

And if we wanted the ``tshirt`` project directory inside that ``src`` directory?

```
/home/username/src/tshirt
```

And if we wanted the ``README.md`` file inside the tshirt project?

```
/home/username/src/tshirt/README.md
```

Going there
-----------

Now that you can create a path, let's go there!

```bash
$ cd /home/username/Documents
```

``cd`` is short for "change directory", and it changes your **current working directory**. Now if you type `ls` you will see the files in your `Documents` folder:
For most terminals, your current working directory is specified in the bash **prompt** mentioned before.

```bash
$ ls
Arduino				android-workspace
Aspyr				eagle
GoPro Projects		gopromote.prm
Hero Lab			nicar_expenses.pdf
Klei				nicar_receipts.pdf
Library				orchard bank travel number.txt
Logan.game			pedagogy.txt
Logans.band			piano 1.band
MapBox				test.txt
NACIS notes.txt		tyler-env.txt
Roblox
```

Navigating relative paths
---------------------

You don't always have to type the full path to a file, otherwise navigation would be very tedious. You can refer to a file by it's path **relative** to your current working directory. For example, from my **home directory** these two paths are equivalent:

```
/home/username/src/tshirt/README.md
src/tshirt/README.md
```

The first path is an ***absolute*** path, meaning that it starts at the **root** of my hard drive, as designated by the leading ``/``. Your root is the base path all files on the OS, there is no superior directory. (Think of it as when you navigate to your C: drive and see your Program Files and Windows directory).

The second path is a **relative** path, meaning that it starts at your current working directory, the same directory that was listed when we ran ``pwd``. So, from your current working directory you move further into another folder relative to where you are at.

Going home
----------

The command line includes a special symbol that always refers to your **home directory**. This is the `~` (tilde). Whenever you type this symbol, it is the same as typing the path to your home directory, so when you need to get back to your home directory, simply type:

```bash
$ cd ~
```

or you can simply just type

```bash
$ cd
```

and it will bring you back to your home directory.

You can also use the `~` to create paths. All of these paths are equivalent:

```bash
/home/username/src/tshirt/README.md
src/tshirt/README.md
~/src/tshirt/README.md
```

What's up there?
----------------

Relative paths and the `~` are not the only ways to save time when constructing paths. It's also important to know about ``.`` and, especially ``..``

`.` always refers to the directory you are in. We will use it to run programs and scripts in our current directory later.

`..` always refers to the directory that *contains* the one your are in, that is, the directory *above* your **current working directory**.

Let's say that I'm working on the ``tshirt`` project and I want to work on the ``commencement`` project. My directories look like this:

```
~
    src
        tshirt
        commencement
```

That is, my **home directory** contains the `src` directory, which contains the ``tshirt`` and ``commencement`` directories. I'm currently working in the ``tshirt`` diretory:

```bash
$ pwd
/home/username/src/tshirt
```

To get to the commencement directory I could type the **absolute path**:

```bash
$ cd /home/username/src/commencement
```

But this is long-winded and I have to remember the entire path. Instead, I can construct a **relative path** using the ``..`` syntax:

```bash
$ pwd
/home/username/src/tshirt
$ cd ..
$ pwd
/home/username/src
$ cd commencement
$ pwd
/home/username/src/commencement
```

Here you can see we've used the ``..`` to move the directory above tshirt and then used a relative path to `commencement` to move into that directory. We can simplify this even further:

```bash
$ pwd
/home/username/src/tshirt
$ cd ../commencement
$ pwd
/home/username/src/commencement
```

In this example we've used the ``..`` to construct a relative path directly to the `commencement` directory. Note that I never had to remember the names of the directories containing these files to get from one to the other.

Tab completion saves the day
----------------------------

Even knowing about `~` and ``..`` you are still going to be typing a lot of folder names. Fortunantly, your **command line** supports **tab completion** for both **commands** and **directories**. To see this in action, type this, but instead of hitting enter after the second command, hit the Tab key where indicated in the second command. (Don't type ``[TAB]``.)

```bash
$ cd ~
$ cd Docu[TAB]
```

Tab completion will auto-complete ``Docu`` to ``Documents/``. **Remember this, you will use it constantly.**

TAB is extremely powerful and something you'll always be using. It is also very benefical if you don't remember the exact program you are looking for. If you press TAB and there are multiple possibilities, your Terminal will list them all. You can even use it for some program's **arguments and sub commands!**

Cancel what you're doing
------------------------

Now if you followed directions you have ``$ cd Documents/`` sitting in your command line. Let's say we don't actually want to run this command. We've changed our mind. To clear this command and reset back to and empty terminal hold the ``control`` key and press ``c``. You'll frequently see this abbreviated ``CTRL+C``.

```
$ cd Documents/[CTRL+C]
$
```

It's useful to imagine that the ``c`` stands for cancel. As you'll see later on, ``CTRL+C`` is often used to exit command line programs, in addition to clearing what things you've typed, but then changed your mind about.

`CTRL+C` however is usually never a clean exit. Think of it like clicking *End Program* in Windows Task Manager, this is will force the program to exit. Usually this is okay and pretty much every program is built to handle this.

Create a file
-------------

You can create an empty file using the ``touch`` command. This can be good for laying out a project with all the files you need or making empty text files to use later.

```bash
$ touch test.txt
```

Now if you run ``ls`` you'll see ``test.txt`` in the list of files.

Delete a file
-------------

To delete a file use the ``rm`` command and provide it's filename as an **argument**.
(remember you can use TAB completion!)

```bash
$ rm test.txt
```

Now if you run ``ls`` you'll see that ``test.txt`` is no more.

This is ***not*** something like right-clicking a file in your File Explorer and selecting "Move to Trash" because there is no Trash Can on the command line. Once you delete it, **it's simply gone.**

Copying and Moving Files
------------------

You will also be copying and moving files regularly. These commands are very straight forward.

To move a file to a directory:

```bash
$ mkdir test
$ touch file1.txt
$ mv file1.txt test/
```

The `mv` command can also rename files.

```bash
$ mv test/file1.txt file2.txt
```

Now, file1.txt was moved to the current directory and renamed to file2.txt!

Copying files works exactly how the move command works, you can change the name of the copied file as well! There is one exception: when copying directories.

```bash
$ cp -r test test2
```

The **flag** `-r` is used to recursively copy the folder directory to a new directory called test2.

Create a directory
------------------

Directories can be created using the ``mkdir`` command. This is equivalent to right-clicking in File Explorer and selecting "New Folder":

```bash
$ mkdir test
```

Delete a directory
------------------

Directories are also deleted with ``rm``, however, you have to do something special to delete a directory:

```
$ rm -r test
```

``-r`` is an example of a **flag**. A flag is a sort of switch that you provide to a **command**. In this case the ``-r`` flag is short for "recursive", which tells ``rm`` to delete both ``test`` and ***everything inside it***.

To summarize:

* ``rm`` is a **command**.
* ``-r`` is a **flag**.
* ``test`` is an **argument**.

**Flags** *usually*, but not always, come before **arguments**.

**Be very careful with the ``-r`` flag**. It's a really easy way to delete a lot of things.

Fun with Wildcards and Globbing
-----------------------

What if I wanted to delete all the JPEGs in directory, but nothing else? First, let's create some dummy JPEG files:

```bash
$ touch test1.jpg
$ touch test2.jpg
$ touch test3.jpg
```

To delete these three files we can use the ``*`` symbol, known as a **glob** or **wildcard**. The ``*`` means "match anything that matches both before and after". Let's look at some examples:

```bash
$ rm *.jpg
```

Lets break that down really quick:

Since we provide the `.jpg` after the `*`, the wildcard will match to everything before the `.jpg`. So if the wildcard will match everything, the `.jpg` will force all those matches to only select the ones that *end in* `.jpg`

This will then delete all files in the **current working directory** that end in ``.jpg``.

```bash
$ rm test*
```

This will delete all *files* in the current working directory that begin with ``test``.
Remember, to delete directories we need the `-r` **flag**.

```bash
$ rm test*.jpg
```

This will delete all files in the current working directory that begin with ``test`` and end with ``.jpg``. This is the safest of these commands as it is the least likely to **accidentally match** something else.

**Be careful when using globs that you don't accidentally delete other things.**

Finding the file you need
-------------------------

Finding files in the command line is an obvious thing you might need to do, and yet even some veteran terminal users can't remember how to do it off the top of their had. The ``find`` **command** is a good example of an exception to the rules of the command line. In particular, it uses **flags** and **arguments** in a different way than the other commands we've looked at. Let's create a file and find it:

```bash
$ touch test.txt
$ find . -name test.txt
./test.txt
```

In this case:

* ``find`` is the  **command**.
* ``.`` is an **argument**, indicating what directory to search in. As you'll recall, `.` always refers to the current directory. This will also search all directories within the current one, the directories within those directories and so on.
* ``-name "test.txt"`` is a **flag** which takes a **value**, in this case, the name of the file you are looking for.

You can also combine ``find`` with the ``*`` to find files that match multiple files, for example:

```
$ touch test1.txt
$ touch test2.txt
$ find . -name test*.txt
./test1.txt
./test2.txt
```

The `find` command is extremely powerful, allow you to even execute another program on all the files it finds! I highly recommend looking up syntax for this command, as it takes some practice to use it properly

Another way to do this that doesn't work on some systems is the `locate` command. This program comes default in Ubuntu/Debian Linux distrubtions and has to be setup on other distrubtions.

This program makes finding files a lot easier, as you simply run:

```bash
$ locate <file/folder name>
```

This will find all files/folder with the name. It will also find near matches if it can't find the exact thing. This is akin more so to the `search` function in most File Explorers.

Learning more about a command
-----------------------------

In addition to searching for filenames you can use ``find`` to search by creation date, update date, file size and more. You can learn more about many command line **commands** by using the ``man`` command:

```
$ man find
```

This will open the builtin documentation for the command, which will explain all the **flags** and **arguments** it accepts. Not all commands have builtin documentation, but most do. Within the documentation you can use your **arrow keys** to navigate by line, `f` (*forward*) and `b` (*forward*) to navigate by pages and ``q`` to quit.

**This is very important!** The man pages will help you learn everything about how to use a program. Usually they are very extensive, but easy to navigate using the hotkeys.

Download a file
---------------

One of the most common things you may need to do is grab a file from the internet so that you can work with it locally. This might be an image, the output of an API or who knows what else. For experimenting, let's download the text of James Joyce's Ulysses from [Project Gutenberg](http://www.gutenberg.org/ebooks/4300).

To do this, we use the `curl` command. The `curl` program allows making pretty much any type of http request you do in browsers. You can use to make scripts to click through websites, download files, and respond to APIs! Lets use it below to download the file.

```bash
$ curl -o ulysses.txt http://www.gutenberg.org/cache/epub/4300/pg4300.txt
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1536k  100 1536k    0     0   736k      0  0:00:02  0:00:02 --:--:--  737k
```

The ``-o`` **flag** allows us to specify the an output filename for the download.

There is also another common program that is made more specifically for downloading files, and thats called `wget`. This, as the name implies (web get) will get files from the web.

```bash
$ wget http://www.gutenberg.org/cache/epub/4300/pg4300.txt -O ulysses.txt
--2020-05-27 01:42:47--  http://www.gutenberg.org/cache/epub/4300/pg4300.txt
Resolving www.gutenberg.org (www.gutenberg.org)... 152.19.134.47, 2610:28:3090:3000:0:bad:cafe:47
Connecting to www.gutenberg.org (www.gutenberg.org)|152.19.134.47|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 660209 (645K) [text/plain]
Saving to: ‘ulysses.txt’

ulysses.txt                                100%[========================================================================================>] 644.74K  1.98MB/s    in 0.3s    

2020-05-27 01:42:48 (1.98 MB/s) - ‘ulysses.txt’ saved [660209/660209]
```

The ``-O`` **flag** is a capital `O` compared to the `curl` command to specify output file name.

Browsing output
---------------

Of all the data formats you might work with: spreadsheets, databases, images; the command line has a particularly robust set of tools for working with **text**. Text in this case means **plain text**, not Word documents, RTF or any other format.

Let's us the the ``cat`` **command** to print the contents of our text file:

```
$ cat ulysses.txt
...
[lots of text here]
...
This Web site includes information about Project Gutenberg-tm,
including how to make donations to the Project Gutenberg Literary
Archive Foundation, how to help produce our new eBooks, and how to
subscribe to our email newsletter to hear about new eBooks.
```

What happened here? All the text of Ulysses just scrolled past on your screen. The ``cat`` command is like ``echo`` except instead of printing whatever **argument** you give it, it prints the **contents of a file**. It doesn't care how long that file is. To browse a long file, we should instead use ``less``:

```
$ less ulysses.txt
<U+FEFF>The Project Gutenberg EBook of Ulysses, by James Joyce

This eBook is for the use of anyone anywhere at no cost and with
almost no restrictions whatsoever.  You may copy it, give it away or
re-use it under the terms of the Project Gutenberg License included
with this eBook or online at www.gutenberg.org
...
[more text here]
...
ulysses.txt
```

Within ``less`` the commands to navigate are the same as within ``man``. Use the **arrow keys** to navigate by lines, `f` (*forward*) and `b` (*backward*) to navigate by pages and ``q`` to quit.

**Note:** there are many types of files which although not strictly **text** are never-the-less *text-based formats*, for example: CSV, YAML and JSON. In general the builtin command line tools are useful for processing these kinds of files as well.

The `cat` or `less` commands can still be used to view the contents of these files, useful for debugging!

Search for text within a file
-----------------------------

``less`` is useful for skimming a text file, but what if you're looking for specific text? That's where ``grep`` comes in:

```
$ grep stately ulysses.txt
stately figure entered between the newsboards of the _Weekly Freeman
Mrs M'Guinness, stately, silverhaired, bowed to Father Conmee from the
```

``grep`` takes two **arguments**: the text to search for and the name of the file to search within. It searches, line-by-line, and outputs every line where that text appears.

You can also use the `-c` flag to output a count of the number of occurences:

```
$ grep -c lovely ulysses.txt
64
```

**Note:** you can't use `*` in your text searches. **Glob** syntax does not work in this case. You must instead use regular expressions (regex), which are beyond the scope of this tutorial. You **can** use the `*` to select multiple files to search!

Search for more
---------------

In the previous examples, we we're searching for only a single word. If we tried to search for multiple words our output would be confusing:

```
$ grep -c lovely socks ulysses.txt
grep: socks: No such file or directory
ulysses.txt:64
```

``grep`` treats every **argument** after the first one as a file to search. In order to search for a phrase, we need to **quote** the search **argument**:

```
$ grep "lovely socks" ulysses.txt
the tie he wore, his lovely socks and turnedup trousers. He wore a pair
```

It's good to get in the habit of quoting text **arguments**. For example, early on you ran:

```
$ echo hello
```
Though this works, it would be better to quote the argument:

```
$ echo "hello world!"
```

The power of redirection
----------------------

You now know enough for me to introduce you to some of the most powerful concepts in the command line: **standard in** and **standard out**. It's helpful to think of these as channels through which data can travel into and out of a program. In the very simplest case, **standard out** is simply what you see printed to the command line after you run a command. You have worked with this before in pretty much any programming language. You also have other outputs like **standard error** which you can use as well!

```bash
$ grep stately ulysses.txt
stately figure entered between the newsboards of the _Weekly Freeman
Mrs M\'Guinness, stately, silverhaired, bowed to Father Conmee from the
```

In this example, lines 2 and 3 are printed to **standard out**. By default, anything printed to standard out appears on the command line after your command. But what if this isn't what we want? What if you wanted to save your results into a file? You need **redirection**:

```bash
$ grep stately ulysses.txt > search.txt
```

The ``>`` is short for "redirect standard out to a file."

This will be very useful when we run our python programs and want to save all the output for debugging purposes later.

```bash
$ cat search.txt
stately figure entered between the newsboards of the _Weekly Freeman
Mrs M\'Guinness, stately, silverhaired, bowed to Father Conmee from the
```

Here we see that the results of our search are now saved in the file named ``search.txt``. If we we're to run this command again it would **overwrite** this file with the same results. What if we wanted to add *more* results to this file?

```bash
$ grep lovely ulysses.txt >> search.txt
```

The ``>>`` is short for "redirect standard out to a file, **appending** to it's contents." If you ``cat search.txt`` now, you'll see it contains our search results for both "stately" and "lovely".

Fun with pipes
--------------

**Standard out** is useful because you can redirect it to files, but it's got another, even more useful function: **redirecting** to **standard in**. As I said before, standard in is a sort of channel through which you can pass data into your **command**. You can't type into this channel, but you can send data into it using the ``|``.

```
$ cat ulysses.txt | grep stately
stately figure entered between the newsboards of the _Weekly Freeman
Mrs M'Guinness, stately, silverhaired, bowed to Father Conmee from the
```

``cat`` reads a file and outputs its contents onto **standard out**. As you'll recall when we did this before it output the entire contents of the file, so we used ``less`` instead. In this case, we output the entire text of the file, but use the ``|`` symbol, which is short for "redirect standard out into standard in". ``grep``, in addition to accepting a filename, can also accept data on **standard in**. Thus these two commands are functionally identical:

```
$ grep stately ulysses.txt
$ cat ulysses.txt | grep stately
```

Because of the symbol, we call this **piping**, but it's just a special kind of **redirection**. Let's say that we want to search for lines that contain the word "mother" and the word "father", but not necessarily together:

```
$ cat ulysses.txt | grep father | grep mother
     My mother's a jew, my father's a bird.
are the dispossessed son: I am the murdered father: your mother is the
excellent idea of affording the poor fatherless and motherless children
a bloody fool to it. Handed him the father and mother of a beating. See
he knows if he's a father or a mother.
from father to, mother to daughter, I mean. Bred in the bone. Milly for
father and thy mother that had the best hand to a rolypoly or a hasty
father and mother of a bating. _(With a tear in his eye)_ All insanity.
Kingsbridge station with his father and mother I was in mourning thats
```

In this example we've **piped** twice, first into a ``grep father`` and then into a ``grep mother`` to further filter the results. Want to save the final results to a file? Just **redirect** the output again:

```
$ cat ulysses.txt | grep father | grep mother > search.txt
```

**Piping can be used to put the output of any program into another!** Not just for grep and text files. There are very powerful "one liners" by piping 4-5 programs together to parse/format output for use in something else. We will save that for another time though.

Saving milliseconds using shortcuts
-------------

As you get used to using pipes you may find that your commands are getting very long. At some point the overhead of making a typo gets very frustrating. You don't want to have to retype very long commands. Fortunantely, you can always go back to a command you executed before by pressing the **up arrow** at the command line. If you press it more than once, you can cycle back through the last commands you've executed. Just hit the ``enter`` key once you've found the one you want.

Sometimes the command you want is one you just entered, but one you entered an hour ago. In this case using the **up arrow** to find it may be very tedious. Instead, hold the ``control`` key and push ``r``. This will put you into "reverse search mode". Simply type in characters that appeared in your original command and terminal will find it. If you've run several commands with similar names you may have to type more characters to uniquely identify it. Once you've found the command you want, press ``enter`` to run it or the **right arrow** to put it into the command line but not run it.

Editing text files
------------

There are many different command line text editors, all with their pros and cons. By far the most easy and widely used is called `nano`. We will use this to view, edit, and create files. Another popular one is called `vim`, that relies on it's own suite of commands/hotkeys. Vim is complicated as it requries a lot of practice to memorize hotkeys/commands, but once learned becomes very powerful.

If you have free time, you can run `vimtutor` which is a full tutorial on how to use every aspect of vim, taught right in the command line!

For now, lets use nano.

```bash
$ nano test.txt
```

This will open a new file in nano with the name `test.txt`. Now, we can type whatever we want.

To save, use the hotkey `CTRL+X`, press `y`, then press `Enter`. *Before pressing enter, you can save the file under a new name by changing the name in the prompt.*

There are hotkeys to search, jump to line, and delete lines. Look at your cheat sheet for commmon ones. Also, at the bottom of the `nano` text editor has some of these listed there for your convience.

To edit a file, simply specify an already existing file.

**Remember, your changes aren't saved until you use the process above to save the file!**

Becoming root
-------------

What does `becoming root` mean? Every Unix system has a user called `root`, which can access every file on the computer, regardless of permissions. We haven't talked about permissions, so not all aspects can be explored right now. But its common that files you created only you have access to, but the root user can see all files.

It can also use certain programs that modifes/access system folders and files, which require root access. On Newton, we can't access the root user, but if you have access to a Unix system you can try it using the `sudo` command.

Sudo allows you to run a single command as the root user. Usually, you'll use it to install programs to the system or access certain files you normally can't access. This is better suited if we all had local Unix systems, so take some time to read about this on your own.


Shell Scripts
------------------

**Bash** was mentioned in the beginning, but what is it? What is a shell?

Put simply, a shell is the *interpreter* for command line instructions. It works very similiarly to how the python *interpreter* reads you python files. Python is a scripting language, and so is shell! You can create full fledge command line programs that is run by shell.

So, whats *Bash* then? Bash is a specific shell that allows for more customization and quality of life features then just a simple shell. Think of it like an IDE for shell (it isn't really though).

We can write script files to automate tasks and combine multiple commands/programs easily then trying to use pipes to do it all in one line.

The codecademy course linked at the beginning goes in depth into this. We will need to understand the very basics for use on Newton, so lets just go over a simple example.

**All shell scripts have the `.sh` file extension, but it isn't required to have it in order to run.**    
Lets use nano to create a simple shell script.

```bash
$ nano myfirstscript.sh
```

**At the beginning of every shell script file, you must add:**
```bash
#!/bin/bash
```

This specfies what shell interpreter to use to run the script, in this case bash is the standard on pretty much every system. You can also use `#!/bin/sh` to use the Unix shell.

Any command line programs, file redirections, piping, and everything else we learned so far can be used directly in the script! The script will run those commands just like if we typed them out in the command line.

Lets add some commands to run, and see how variables work.
```bash
#!/bin/bash

# I am a comment!
echo "Hello, World!"

echo "Current working directory:"
pwd

myVar="Hello!"

echo $myVar
```

In this script, we use echo to print some strings, and run the `pwd` command to print the current working directory.    
We also define a variable called `myvar`, which contains a string "Hello!". There aren't datatype specification, just like python. To access a variable's content though, we must put a `$` in front of the variable name.

Now, save the file.

**How do we run the script?**    
To run a script in the current directory, you put `./` and then the script name.

```bash
$ ./myfirstscript.sh
```

You'll notice you get a `Permission Denied` error! Why is that? Well, the file needs to be marked as "executable" so that your OS knows that this is an executable file. To do that, run

```bash
$ chmod +x myfirstscript.sh
```

Chmod is used to change file permissions, but don't worry too much about that for now.

Now we can run the script.

```bash
$ ./myfirstscript.sh
Hello, World!
Current working directory:
/home/username
Hello!
```

As we can see, we saw the lines printed, our current working directory printed, and the variable we defined displayed.

Take some time to mess around with this more, try running the commands we used in this tutorial in the scripts. There is so much more to bash scripting, but this tutorial is already extremely lengthy, so we will stop here.
