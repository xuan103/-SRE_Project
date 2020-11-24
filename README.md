# Class1-Tue (11/24)

- objective

![](https://i.imgur.com/9ShGlQo.png)


- test

    - ans

- Please follow the instructions

1. use `wget` to download `github.com` page.
2. cope the `.html` to /wk, and give new name `github.com`.
3. make a new folder `mysc`.
4. move the `github.com` to `/mysc`.
5. use `tree` to see the structure.
6. remove `mysc` folder.

> wget github.com 
> ls
> cp index.html ~/wk/github.com
ls
> mkdir mysc
> mv github.com ~/wk/mysc/
> tree
> rm -r mysc/
ls

---

 「家目錄」中哪個檔案是儲存指令歷史紀錄？

![](https://i.imgur.com/3BleVQe.png)

- ans 

    - .bash_hortoy

    
如何顯示全部指令歷史紀錄？

- ans 

> hortoy

> cat  ~/.bash_history

- hortoy 暫存在記憶體
- .bash_history 儲存在硬碟（SSD or HD）

---

# History

> history --help

```
history: history [-c] [-d offset] [n] or history -anrw [filename] or history -ps arg [arg...]
    Display or manipulate the history list.
    
    Display the history list with line numbers, prefixing each modified
    entry with a `*'.  An argument of N lists only the last N entries.
    
    Options:
      -c	clear the history list by deleting all of the entries
      -d offset	delete the history entry at position OFFSET.
    
      -a	append history lines from this session to the history file
      -n	read all history lines not already read from the history file
    		and append them to the history list
      -r	read the history file and append the contents to the history
    		list
      -w	write the current history to the history file
    
      -p	perform history expansion on each ARG and display the result
    		without storing it in the history list
      -s	append the ARGs to the history list as a single entry
    
    If FILENAME is given, it is used as the history file.  Otherwise,
    if HISTFILE has a value, that is used, else ~/.bash_history.
    
    If the HISTTIMEFORMAT variable is set and not null, its value is used
    as a format string for strftime(3) to print the time stamp associated
    with each displayed history entry.  No time stamps are printed otherwise.
    
    Exit Status:
    Returns success unless an invalid option is given or an error occurs.
```
- 清除歷史紀錄

> history -c

> history -a

> history -n

> date +%F-%l-%M-%S

```
2020-11-24-11-07-33
```

> export HISTTIMEFORMAT="%Y-%m-%d-%H-%M-%N, "


> nano ~/.bashrc

```
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
## HISTCONTROL=ignoreboth
...

## ADD HISTORY TIME
export HISTTIMEFORMAT="%Y-%m-%d-%H-%M-%S, "
```

> . ~/.bashrc

- ( or add new windows, and source ~/.bashrc

- ( if server ?
 
    - try exit

---


- test1

    - ans

1. 建立一個指令清單命名為 `command.list`
2. 用指令清單與 `.bash_history` 比對正確指令與錯誤指令

> cat command.list

```
ll
ls
cat
grep
cut
awk
df
gree
lscpu
..
```

> nano check_history.sh

```
#!/bin/bash

okk=0
nno=$(cat -n ~/.bash_history | tail -n 1 | cut -d $'\t' -f 1) 


while read history;do
 while read command;do
  echo $history | grep $command > /dev/null

   if [ $? == "0" ];then
    okk=$(($okk+1))
    break
   fi
 done < command.list
done < ~/.bash_history

ans=$(($nno-$okk))

echo "ok: $okk"
echo "no ok: $ans"
```

> echo $(cat -n ~/.bash_history | tail -n 1 | cut -d $'\t' -f 1)

```
902
```

> ./check_history1.sh 

```
ok: 784
no ok: 118
```

- last bigred


---

# inotify

- install inotify 

> sudo apt update

> sudo apt install inotify-tools

> inotifywait --help

```
xuan@xuan-lenovo:~/wk$ inotifywait --help
inotifywait 3.14
Wait for a particular event on a file or set of files.
Usage: inotifywait [ options ] file1 [ file2 ] [ file3 ] [ ... ]
Options:
	-h|--help     	Show this help text.
	@<file>       	Exclude the specified file from being watched.
	--exclude <pattern>
	              	Exclude all events on files matching the
	              	extended regular expression <pattern>.
	--excludei <pattern>
	              	Like --exclude but case insensitive.
	-m|--monitor  	Keep listening for events forever.  Without
	              	this option, inotifywait will exit after one
	              	event is received.
	-d|--daemon   	Same as --monitor, except run in the background
	              	logging events to a file specified by --outfile.
	              	Implies --syslog.
	-r|--recursive	Watch directories recursively.
	--fromfile <file>
	              	Read files to watch from <file> or `-' for stdin.
	-o|--outfile <file>
	              	Print events to <file> rather than stdout.
	-s|--syslog   	Send errors to syslog rather than stderr.
	-q|--quiet    	Print less (only print events).
	-qq           	Print nothing (not even events).
	--format <fmt>	Print using a specified printf-like format
	              	string; read the man page for more details.
	--timefmt <fmt>	strftime-compatible format string for use with
	              	%T in --format string.
	-c|--csv      	Print events in CSV format.
	-t|--timeout <seconds>
	              	When listening for a single event, time out after
	              	waiting for an event for <seconds> seconds.
	              	If <seconds> is 0, inotifywait will never time out.
	-e|--event <event1> [ -e|--event <event2> ... ]
		Listen for specific event(s).  If omitted, all events are 
		listened for.

Exit status:
	0  -  An event you asked to watch for was received.
	1  -  An event you did not ask to watch for was received
	      (usually delete_self or unmount), or some error occurred.
	2  -  The --timeout option was given and no events occurred
	      in the specified interval of time.

Events:
	access		file or directory contents were read
	modify		file or directory contents were written
	attrib		file or directory attributes changed
	close_write	file or directory closed, after being opened in
	           	writable mode
	close_nowrite	file or directory closed, after being opened in
	           	read-only mode
	close		file or directory closed, regardless of read/write mode
	open		file or directory opened
	moved_to	file or directory moved to watched directory
	moved_from	file or directory moved from watched directory
	move		file or directory moved to or from watched directory
	create		file or directory created within watched directory
	delete		file or directory deleted within watched directory
	delete_self	file or directory was deleted
	unmount		file system containing file or directory unmounted

```

> inotifywait -m ~

```
Setting up watches.
Watches established.
```

> inotifywait -mr ~

```
Setting up watches.  Beware: since -r was given, this may take a while!
Watches established.
/home/xuan/.config/libreoffice/4/user/ CREATE XkFqMC
/home/xuan/.config/libreoffice/4/user/ OPEN XkFqMC
/home/xuan/.config/libreoffice/4/user/ MODIFY XkFqMC
/home/xuan/.config/libreoffice/4/user/ MODIFY XkFqMC
```


> inotifywait -mr --event modify ~

```
Setting up watches.  Beware: since -r was given, this may take a while!
Watches established.
/home/xuan/.config/google-chrome/Profile 1/ MODIFY .com.google.Chrome.4mEQHn
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies-journal
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies-journal
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies-journal
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies-journal
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies-journal
/home/xuan/.config/google-chrome/Profile 1/ MODIFY Cookies
..
```
 
> inotifywait -mr ~ --format '%T,%w,%f,%e' --timefmt '%Y-%m-%d-%H-%M-%S' 

```
Setting up watches.  Beware: since -r was given, this may take a while!
Watches established.
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,.com.google.Chrome.ex7MLc,CREATE
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,.com.google.Chrome.ex7MLc,OPEN
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,.com.google.Chrome.ex7MLc,MODIFY
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,.com.google.Chrome.ex7MLc,CLOSE_WRITE,CLOSE
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,.com.google.Chrome.ex7MLc,MOVED_FROM
2020-11-24-15-38-41,/home/xuan/.config/google-chrome/Profile 1/,Preferences,MOVED_TO
..
```

> sudo inotifywait -mr ~ --format '%T,%w,%f,%e' --timefmt '%Y-%m-%d-%H-%M-%S'

> ps -ef | grep inotifywait



