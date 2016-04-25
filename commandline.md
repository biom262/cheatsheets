http://www.emoji-cheat-sheet.com/

# Command line cheatsheet

This is a cheat sheet for "Bourne-again shell" (bash) and GNU/Linux commands.
## The basics:
```
	cd ~ 					go to home directory
	clear					clear terminal
	source					reload this file
	mkdir					make directory
	rm -rf					Remove recursively and force
	chmod ###				777 is the most generous, order:  user, group, everyone
	more					Contents shows up below printed to terminal
	less					bring up the document, but remove it from the screen and terminal on Q
	cat					can view (print to terminal) can also pipe data to other things, concatenate vertically
	>					Output to a file (can replace the file if it already exists) Called "redirecting"
	>> 					append to existing file (does not replace existing file, only appends)
	mv					Cut and Paste (copy but remove source)
	cp					copy (retain source)
	tree -f					prints out a directory structure tree (stop doing cd ls recursively!) -f option prints full paths on every branch
	du -ch					display disk use (-c sum all subfolders) (-h make human readable)
	ln -s					soft links	Source Destination
	which 					find the path to the object specified
	ls -lhaH				human-readable sizes, permissions, groups, and symbolic links displaying fullpaths
	qstat -r hotel | less			Show all jobs on the group “hotel”
	qstat -u ucsd-train03			show job status list for the user “ucsd-train03”
	qsub script.sh				submit script (which has PBS flags) to the queue
	tee					Write to something and submit it to a program (see manual)
	who					On TSCC this lists all of the logged in users and their IP addresses
	pwd -P					Print physical directory "what drive am I on?"
	top				What jobs are running?
	screen				See: https://kb.iu.edu/d/acuy
	git ls-files			Which files are managed by git
	git ls-files -u			Which files are unmerged or have conflicts
	git log				show all commits that have happened with these files (use to find commit hash if you want to reset)
	git reset (hash)		reset to certain commit (find hash with git log)
	git checkout --ours		use everything that is yours when you merge
	git checkout --theirs		use everything that is theirs when you merge
	
	Mac-Only: 
	hdiutil attach /path	Mount a disk image via command line
	hdiutil info			Tell me about mounted disk images
	hdiutil detach /dev/disk1s2	detach image at specified location if you don't know it, use the info command^
	
```
## How basic program inputs and outputs work:
Standard in
to
#####Program
outputs two outputs
###### 1) Standard out (stdout)
###### 2) Standard Error (stderr)

You can redirect either or both E.G. ```grep NFKB tfs.bed 1>tfs.nfkb.bed 2>tfs.nfkb.err```

If you don't specify the stream that you want it will just redirect the standard out and alternatively print the standard error output to the command line (E.G. grep:tfs.bed: No such file or directory).

###Advanced:
On TSCC if you write  
2>&1
as the last line, you will cat your standard error and out files to standard out

#TSCC
when you are in your home directory on the login node, you are writing to the login node, the login node has very little available ram per user (5 login nodes * 64GB /#Users)

when you are in your scratch directory, you are writing to the scratch directory

when you submit to qsub, you are assigned a host machine based on your request (#PBS scripts) for processors, memory, etc. AT THE MOMENT YOU MAKE THE REQUEST. If other tasks go out of control and start taking up more memory it can fuck things up quickly for everyone on that host.

If you disconnect from the login node while writing to Scratch, it cuts off whatever you are currently writing and leaves a corrupted file.  USING THE HEAD NODE IS RISKY. If they want to install an update and bring down your login node, you're boned. If you've submitted your stuff as a job you are far more protected.


## Text maninpulation
### Navgiation 
```
Control + E 		Jump to end of the line
Control + A		Jump to start of the line
Alt + ErrorKeys		Jump between words on the same line
```
## Skip the first line of a file

```
tail -n +2 filename.txt
```


## Compressing and decompressing files

### Create a gzipped tarball from a `*` glob command

This will create the gzipped tarball `voyages_different_direction.tar.gz` with everything in my current folder that starts with `voyages_different_direction_`.

* `z` is for "g***z***ip"
* `c` is for "***c***ompress"
* `v` is for "***v***erbose." Show all the files that are getting compressed as it's happening. (Good for debugging)
* `f` is for "***f***ilename." Must be the last flag because the filename is inferred after it

```
tar -zcvf voyages_different_direction.tar.gz voyages_different_direction_*
```

### Decompress a gzipped tarball

The `-` is optional, can also say `tar -xzvf`

* `x` is for "e***x***tract"
* `z` is for "g***z***ip"
* `v` is for "***v***erbose." Show all the files that are getting extracted as it's happening. (Good for debugging)
* `f` is for "***f***ilename." Must be the last flag because the filename is inferred after it

```
tar xzvf voyages_different_direction.tar.gz
```

### Create a zip file

Create a zip file from multiple files

```
zip squash.zip file1 file2 file3
```


## File management

### Soft links

Make a pretend file in a new place that jumps to the old file when you look at it.

```
ln -s oldfile newplace
```

### Hard links

Make a new pointer (aka `inode`) to the same data in the filesystem. If there are multiple pointers to the data, the data is not truly "erased" until the last pointer is removed (i.e. the `inode` number is decremented to 0)

```
ln oldfile newplace
```

### Opening files containing and not containing certain text

Open all files that match this glob command, except if they have `stream` in them.

```
ls -1 exon_3p_exonbody_tier1_*/homerResults.html | grep -v stream | xargs open
```

## Installing programs 

### Python

To install a python program, usually you can use `pip`. I like the `-e` flag, which installs it in "developer mode" because any change in the files located there is also reflected in the imported code.

```
cd python_program/
pip install -e .
```

### From source

To install a program from source on a shared cluster, you will often need to specify the directory you want to install things. That can be done with `--prefix` at the `./configure` step. The directory is where you want all the `bin/`, `lib/` and so on files to be added to (default is `/usr/local`) Then, do `make` and `make install`. For example:

```
./configure --prefix=/projects/ps-yeolab/software
make && make install   # "make install" will run only if "make" is successful
```

### Screens
Always use the same login node!
```
screen -S try1
```
[detached]

```
screen -S try2
```
[detached]

```
screen -x
```
There are several suitable screens on:
	40357.try1	(Detached)
	40451.try2	(Detached)

Type 
```
screen [-d] -r [pid.]tty.host" 
```
to resume one of them.
```
screen -x 40357.try
```
[screen is terminating]

```
screen -x
```
[screen is terminating]


### .bashrc

#####This is where you put all of the stuff you want to execute, load, echo, etc when you login to TSCC!
```
nano ~/.bashrc
```

##### On your local machine:
```
nano ~/.bash_preferences
```
##### For using "screens"
```
nano ~/.screenrc
```

### Installing biotools
```
mothership:~ mbaughn$ pip install biotools
Collecting biotools
  Downloading biotools-1.2.12.tar.gz
Building wheels for collected packages: biotools
  Running setup.py bdist_wheel for biotools ... done
  Stored in directory: /Users/mbaughn/Library/Caches/pip/wheels/86/0e/ec/57f244b7a14c7847b55636f1900206b5039b38828372cafd87
Successfully built biotools
Installing collected packages: biotools
Successfully installed biotools-1.2.12
```

##### Windows Firewall -- Block an Individual IP Address:
```
netsh advfirewall firewall add rule name="Intuit1" dir=out interface=any action=block remoteip=75.98.51.173
```
