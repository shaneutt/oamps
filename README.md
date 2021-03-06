OAMPS (OpenArena multi-purpose servers launcher)
================================================

OAMPS (OpenArena multi-purpose servers launcher) is an automatic server management tool to be used with Cron. Includes GTV (GamersTV) management features.

DESCRIPTION
-----------

This script is made to ease the life of any game server administrator of any Quake 3 Arena based game (particularly for OpenArena, but should work for any other).

What this script can do for you :

  .it helps you construct perfect commandlines

  .ease the manual maintenance by bringing down the level of complexity of your bash file (yes you can bash this script, and even output to a file !)

  .you can directly manage everything with this script (no need for manual line coding)

  .adds advanced functionnalities unavailable otherwise, like batching some automatic /rcon say commands.

  .can automatically add itself to a cronjob

  .detects server crashs and can act automatically

  .notify you of everything that happens (at screen or in a log)

  .2 modes for launching servers : in a background process, or in a screen. All functionnalities are available for both of these modes

  .logs rotation

  .gtv management

  .gtv connection with master listing server (shows your gtv server in the in-game servers browser)

  .gtv auto room renaming

And so many others, just type "sh oamps.sh --help" to get a full list of the features and some examples.

To summary : if you're looking for a tool to automatically manage and maintain your servers, cleaning and restarting them everyday, watching over crash and such stuffs, then you've come over the right tool : OAMPS.SH !

LICENSE
-------

Oamps is licensed under the GNU Lesser General Public License (LGPL) v3 or above.

BASIC USAGE
-----------

    # chmod +x oamps.sh
    # ./oamps.sh --help

More examples will be given in the help (oamps.sh --help), or look in the samples folder to find some batch examples.

GOING FURTHER
-------------

You can use monit to enhance functionnalities (like web remote start/stop) or to add a security layer.
This script is compatible with Big Brother Bot >= 1.3.4 (it's compatible with previous versions as well, but the rotate log function will break B3 until you pause/unpause it or restart it, this bug should be fixed in newer B3 versions).

FAQ
---

- I saw it is possible to send commands to a server, is it possible to send commands to a remote server via rcon?

No. The purpose of oamps is to manage servers, and you can't do that remotely, you need to execute oamps on the same computer and user account as the servers to manage them.

Although oamps provide some functions to send commands to servers (such as --exec and --execdelay), they are merely provided as a mean to ease servers administration by automating some tasks, but remotely accessing a server via rcon is not in this scope. Plus, oamps only sends commands, it does not show any feedback or result.

If you want to remotely manage your server via rcon, there are a lot of tools to do just that, such as crcon (opensource, http://sourceforge.net/projects/crcon/).

- [[: not found
Just use ./oamps.sh or bash oamps.sh instead of sh oamps.sh, this should fix your problem.

- Cannot launch a server
First make sure you chmodded both oamps.sh and your OA server binary:
chmod +x oamps.sh
chmod +x oa_ded.*

If you still cannot launch a server, you can either check your gamelog (g_log) to see what happened and if there were any errors, or use oamps.sh -v to get a verbose information, which will tell you the full commandline that is used to launch your server.
Then copy the commandline and try to launch the server by yourself to see the direct output.

- Server crash: couldn't load default.cfg
default.cfg is located inside the primary pak: pak0.pk3 . So if you get this error, this simply means that your binary could not find the paks. You should check your basepath and homepath if you specified +set fs_basepath or +set fs_homepath (the baseoa folder, containing the pk3, should reside INSIDE the homepath or basepath, so if you set basepath to "baseoa", you have to put your pk3s inside baseoa/baseoa/*.pk3 )

- ALWAYS try "vstr nextmap" to check that the server finds all maps in rotation, else it will cause a bug where at the end of the game, the game will restart on the same map but players will be stuck at the waiting score screen!

- My GTV server can't be seen in dpmaster, and hping3 doesn't work!
Hping3 requires to be used as root because it sends raw udp packets (and unix users can't do that), so you have to setup a SUDO setting to allow to sudo hping3 without asking for password.
To do that, you can use visudo, and then write something like this: user ALL = NOPASSWD: /bin/hping3


EXTENDED USAGE
--------------

Launch an OpenArena server (or any q3 based game) with pre-configured features. Include GTV (GamersTV) management features.

  -a, --addcron          add a cron job for the current arguments (will store ALL arguments into current user's cron). Value is cron date (in the form mm hh jj MMM JJJ or @something (@weekly, @daily, @hourly, @reboot, etc...)

  -b, --basepath         set the base (root) path of your game server's files (binaries, paks, ressources, etc...). This is used to make your game server believe it resides in another folder than where it is executed from (used for cron jobs too since cron needs absolute paths). Default to the folder where the game server binary resides.
  Note : basepath should be used for GTV instead of homepath (because GTV doesn't redirect everything to homepath, for example the delay folder).
  Note2: see --homepath, an extension of --basepath.

  -bin, --binfullpath    specify the server binary (permits to launch different games servers) - Note: basepath and homepath will be relative to the binary location

  -c, --config           use this config (_NO_ .cfg extension [eg: -c yourconfig]). Will append a number if -n or take all configs with the same pattern in the folder if -m is specified (eg: for \"oacl-config\" it will generate oacl-config0.cfg, oacl-config1.cfg).
  Note : Extension of your config must be .cfg ! eg: yourconfig.cfg
  Note2 : Your config must reside in the basepath/basemod (=baseoa) or homepath/basemod or homepath/mod folder! Absolute paths are NOT supported (by game engine)!
  
  -ct, --countdown		 say a countdown (in seconds) to the server before doing anything (should be used with -r, -k or -k2, but not only). The time will be automatically divided by 2 for each say. Eg: --countdown 60 will warn players 1 minute before the restart, then 30 sec, then 15 sec, then 7 sec, then 3 sec, then 2, then 1 second.
  
  -ctm, --countdownmessage	 custom message for countdown in the format 'say Warning: %countdown before restarting!' - default: 'say ^3Warning: the server will be restarted for maintenance in %countdown'

  -e, --exec             exec directly a command to the server (server is found via --config or --screenname)
  
  -ec, --execconfig      exec an external config file, just like commands were inputted directly inside the console (eg: yourconf.cfg)
  Note : path can either be absolute, or relative to this script directory.

  -ed, --execdelay       add a delay before executing the external config file
  
  -ext, --extend         add your own custom commandline arguments (eg: --extend \"+set mapname hihi +cvar_restart\")
  Note : custom commands will be executed after the normal commands that this script generates.
  
  -f, --fixgravity       fix the gravity for OA vanilla only, to compensate with the use of pmove_float

  -g, --gamemod          specify a gamemod (eg: baseoa, cpma, oacl2010). Defaults to baseoa. NEVER set this with GTV, leave it empty !

  -gb, --gamebasemod     specify a game base mod (eg: baseoa for OpenArena). Used by game servers for path in files searching.

  -h, --homepath         set the home path of media files (configs, paks, models and maps). By default, the same as where the game server binary resides, but by setting it you can specify a folder where all the pak and maps resides different than your game folder basepath, useful if you don't have admin rights (ie: game folder at /usr/lib/openarena and your own folder that you will set as homepath at /home/yourlogin/.openarena)
  Note : OpenArena home resides in ~/.openarena by default, this behaviour is changed here

  -hb, --heartbeat       activates regular heartbeats to the master listing server (used for GTV so it stays in the listing).

  -hs, --heartbeatscreen sets heartbeat screen name, if not specified, it will be launched in another terminal.

  -ht, --heartbeattime   sets the delay between each heartbeat to the master listing server (in seconds). Default : 5 min (300 sec).

  -k, --killall          kill all game terminals containing a config name similar in --config, or all screen sessions containing a similar name in --screenname. This will be done prior launching any server.
 
  -k2, --killall2        kill ALL game terminals, or ALL screen sessions (if using --screenname. Eg: -k2 -s), without any distinction.

  -j, --junk             clean the junk server config files created by CPMA or OA (q3config_server.cfg) and do a cvar_restart, and can clean the delay temp folder of GTV.

  -lan, --lan            set dedicated 1, no heartbeat to internet master listing server, your server will only be accessible via LAN
  
  -l, --log              specify the log used by your game server, to enable log rotation based on max size (rotation based on creation date is impossible on Unix)
  Note : big logs are known to produce lags. It is strongly advised to rotate your server's log.
  
  -lm, --logmaxsize      cut down the log logs to x KiloBytes (default: 5000KB = 5MB)

  -lof, --logoutputfolder backup the outputted logs in the specified folder (ordered by config name, date and time)

  -m, --multiple         run as many servers as the number of configs there are, screenname and ports are incremented (eg: '-m --config oacl-cpma' will launch a server for oacl-cpma-ctf.cfg , oacl-cpma-insta.cfg , oacl-cpma-elim.cfg , etc...)
  
  -n, --number           number of servers to run, ports and screenname are incremented (eg: '-p 10000 -s screen -n 3 -c oacl-cpma' will launch a server with oacl-cpma0.cfg on screen0 port 10000, a server with oacl-cpma1.cfg on screen1 port 10001, a server with oacl-cpma2.cfg on screen2 port 10002) 

  -o, --outputlog        saves all output of this script into the specified log
  
  -p, --port             port of your server. Will be incremented when using --number and --multiple

  -r, --restart          force restart : kill each server before relauching it

  -s, --screenname       screen session name for each servers (if not supplied another detached terminal will be opened)
  
  -s2, --screenname2     set the screen session name based on the config name (no need to supply a value here). Eg: --config oacl-cpma1.cfg will set screen oacl-cmpa1.cfg
  Note : with --multiple option, the full name of the config files found will be used.

  -tv, --gtv             enable gtv launching (one at a time).
  
  -tvb, --gtvbasepath    gtv basepath - useful to tell GTV where to find basemod folder (baseoa) and config folder (gtv3)
  
  -tvh, --gtvhomepath    gtv homepath - Note: homepath should be the same as homepath, for GTV to find the required mod's pk3s, but NOT to find basemod folder nor GTV config files (look for gtvbasepath)!

  -tvc, --gtvconfig      gtv config (with extension!), defaults to gtv.cfg if argument not supplied
  Note: must reside in gtvbasepath/gtv3 folder! It won't work in any other place (nor in gtvhomepath, contrary to homepath for game servers!)
  
  -tve, --gtvexec        exec directly a command to the GTV server (server is found via --gtvonfig or --gtvscreenname)
  
  -tvec, --gtvexecconfig exec an external config file for GTV, just like commands were inputted directly inside the console

  -tved, --gtvexecdelay  add a delay before executing the external config file for GTV

  -tvm, --gtvmaster      apply a trick to register to the supplied master listing server (you can see the GTV server in the game browser !)
  Note : the trick uses hping3, so you need it to be installed on your system for the trick to work, and since hping3 is a root tool, it will ask your user (not root!) password to issue a sudo hping3 !
  Note2: you can also use hping v1, but NOT hping2 which contains a bug that prevent the script from working on certain systems.

  -tvmp, --gtvmasterport port of the master listing server

  -tvmd,--gtvmasterdelay delay after launching the GTV server, before sending the heartbeat (GTV has to be fully started for the heartbeat to work !). Default to 5 sec.

  -tvp, --gtvport        set this port for GTV (if blank then GTV will take the last port, after all servers were launched)

  -tvs, --gtvscreenname  screen session name for GTV (if not supplied another detached terminal will be opened)

  -tvbin, --gtvfullpath  full pathname to the gtv binary (included the binary name and extension !) - Note: gtv basepath and homepath will be relative to the binary location
  
  -v, --verbose          show more informations, mainly for debugging purposes
  
  --version              show the version

  -vm, --vmgame          set the vm_game property (only useful for mods !). 0 = load aside on its own memory, 1 = load normally, 2 = load in the same memory as the core game
  
  -w, --watch            set the time (in seconds) to considerate the crashlog.txt (a file created when your server crashes but still stays in memory) as new enough to relaunch the server. -1 : Crashlog Watching Disabled, 0 : Unlimited, > 0 : timeframe to considerate the crashlog as valid to relaunch the server (Default : 24h = 86400).
  Note : the crashlog.txt file gets renamed after the server has been restarted, so the server can't be restarted twice for the same crash.
  

      --help      show this help

      
General Notes
-------------

  All paths are relative to the script location (this is to avoid problems when calling this script from cron jobs).

  
Exemples
---------

  ./$SCRIPTNAME -c oacl-edit-me -g oacl2010 -p 27960 -s oaclserver --- launch a server with gamemod oacl2010 and config oacl-edit-me.cfg and port 27960 on screen named oaclserver.

  ./$SCRIPTNAME -c oacl-edit-me -g oacl2010 -p 27960 -s oaclserver -n 3 --- launch 3 servers with gamemod oacl2010 and config oacl-edit-me0.cfg then oacl-edit-me1.cfg then oacl-edit-me2.cfg and port 27960-27962 on screens named oaclserver0 to oaclserver2.

  ./$SCRIPTNAME -c oacl-edit-me -g oacl2010 -p 27960 -s oaclserver -r -a @daily --- kill a server and restart it, then add a cron job to automatically restart this server daily.

  ./$SCRIPTNAME -c oacl-edit-me -g oacl2010 -p 27960 -s oaclserver -a @hourly --- launch the server, then add a cron job to try each hour to relaunch this server (without -r the script don't kill the server, it relaunch it only if it doen't already exists).

  ./$SCRIPTNAME -n 0 -hb -tv -tvp 31000 -tvc gtv.cfg -tvm dpmaster.deathmask.net -tvmp 27950 --- launch no game server, only a GTV server with port 31000 and config gtv.cfg and register to the master listing server of OpenArena and send regular heartbeats to stay on the servers list.
  
  ./$SCRIPTNAME -n 0 -c oacl-edit-me -s oaclserver -ec 'morecommands.cfg' -ed 5 --- wait 5 seconds and sends all the commands contained inside 'morecommands.cfg' to the server at screen 'oaclserver'
  
  ./$SCRIPTNAME -n 0 -k -s oaclserver --- kill all servers with a screen name containing 'oaclserver'



CREDITS
-------

Thank's to Heap for supporting me.

Thank's to my woman for not becoming crazy when I became obsessed with my work, and for her unbreakable and thoughtful affection. I love you, you wonderful being.
