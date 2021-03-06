isohunt-grab
=========================

**isoHunt is dead, long live isoHunt!** You may shut down your archiving scripts; [isoHunt is no more](http://torrentfreak.com/isohunt-shuts-down-early-to-block-backup-plan-131021/). The current estimate is that we managed to scrape around 750,000 torrents with metadata. Archives will be announced in #archiveteam on irc.efnet.org, and uploaded to archive.org. Thanks for your help, everybody!

If you want to talk to other people involved, we're still in the #isoprey channel on EFNet.

Seesaw script for the Archive Team Isohunt grabbing.
You'll find this project on the Archive Team Tracker (http://tracker.archiveteam.org/isoprey/).

Seeing a lot of 404 errors in your terminal when running this script is normal. As long as they're just 404 errors, all is fine. Questions, comments, suggestions? Feel free to drop by in #isoprey on EFNet.

**Do __not__ try to run multiple instances of the pipeline script on the same IP. The pipeline is hardcoded to a maximum concurrency of 10 threads; above this, Isohunt will __ban your IP__.** Just copypaste the pipeline commands below, and you will be fine.

Why we're archiving Isohunt
-------------------------

Isohunt is [shutting down](http://torrentfreak.com/isohunt-shuts-down-after-110-million-settlement-with-the-mpaa-131017/). We're trying to save not just the torrents, but also metadata around it, such as user comments.

Why aren't you just crawling for valid torrents? This is inefficient.
-------------------------

Isohunt has (low) hard limits for the amount of search and browse results it returns. Attempting to download every single ID is the only way to ensure that we're not missing anything.

Setup instructions
=========================

Be sure to replace `YOURNICKHERE` with the nickname that you want to be shown as, on the tracker. You don't need to register it, just pick a nickname you like.

In most of the below cases, there will be a web interface running at http://localhost:8001/. If you don't know or care what this is, you can just ignore it - otherwise, it gives you a fancy view of what's going on.

**If anything goes wrong while running the commands below, please scroll down to the bottom of this page - there's troubleshooting information there.**

Running with a warrior
-------------------------

This project isn't in the ArchiveTeam Warrior project list yet. This means that it won't run from the regular interface. Running Windows or want to run it in the Warrior for another reason? You can still do it manually.

If you want to run this on Linux, then skip ahead to the next section; otherwise, read on.

1. Download and set up the [ArchiveTeam Warrior](http://www.archiveteam.org/index.php?title=ArchiveTeam_Warrior).
2. After starting the Warrior VM, make sure that your keyboard input is captured and press `ALT + F3`. You will get a terminal.
3. Log in with the login details you're shown on the terminal (the username is `root`).
4. `apt-get update`
5. `apt-get install -y git-core libgnutls-dev lua5.1 liblua5.1-0 liblua5.1-0-dev screen python-pip bzip2`
6. `git clone https://github.com/joepie91/isohunt-grab.git; cd isohunt-grab; ./get-wget-lua.sh`
7. `run-pipeline pipeline.py --concurrent 10 --port 8002 YOURNICKHERE`

Running without a warrior
-------------------------

To run this outside the warrior, clone this repository and run:

    pip install seesaw
    ./get-wget-lua.sh

then start downloading with:

    run-pipeline pipeline.py --concurrent 10 YOURNICKHERE

For more options, run:

    run-pipeline --help
    
Running multiple instances on different IPs
-------------------------

Follow the instructions for setting up dependencies on your distribution below, but instead of running run-pipeline directly, use multi.sh in the repository. 

Put a list of IPs in a file named `ips`. Don't forget to provide the nickname you want to use as the first argument!

Distribution-specific setup
-------------------------

### For Debian/Ubuntu:

    adduser --system --group --shell /bin/bash archiveteam
    apt-get install -y git-core libgnutls-dev lua5.1 liblua5.1-0 liblua5.1-0-dev screen python-pip bzip2
    pip install seesaw
    su -c "cd /home/archiveteam; git clone https://github.com/joepie91/isohunt-grab.git; cd isohunt-grab; ./get-wget-lua.sh" archiveteam
    screen su -c "cd /home/archiveteam/isohunt-grab/; run-pipeline pipeline.py --concurrent 10 --address '127.0.0.1' YOURNICKHERE" archiveteam
    [... ctrl+A D to detach ...]
    
### For CentOS:

Ensure that you have the CentOS equivalent of bzip2 installed as well. You might need the EPEL repository to be enabled.

    yum -y install gnutls-devel lua-devel python-pip
    pip install seesaw
    [... pretty much the same as above ...]

### For openSUSE:

    zypper install liblua5_1 lua51 lua51-devel screen python-pip libgnutls-devel bzip2 python-devel gcc make
    pip install seesaw
    [... pretty much the same as above ...]

### For OS X:

You need Homebrew. Ensure that you have the OS X equivalent of bzip2 installed as well.

    brew install python lua gnutls
    pip install seesaw
    [... pretty much the same as above ...]

**There is a known issue with some packaged versions of rsync. If you get errors during the upload stage, isohunt-grab will not work with your rsync version.**

This supposedly fixes it:

    alias rsync=/usr/local/bin/rsync

### For Arch Linux:

Ensure that you have the Arch equivalent of bzip2 installed as well.

1. Make sure you have `python-pip2` installed.
2. Install [https://aur.archlinux.org/packages/wget-lua/](the wget-lua package from the AUR). 
3. Run `pip2 install seesaw`.
4. Modify the run-pipeline script in seesaw to point at `#!/usr/bin/python2` instead of `#!/usr/bin/python`.
5. `adduser --system --group --shell /bin/bash archiveteam`
6. `screen su -c "cd /home/archiveteam/isohunt-grab/; run-pipeline pipeline.py --concurrent 10 --address '127.0.0.1' YOURNICKHERE" archiveteam`

### For FreeBSD:

Honestly, I have no idea. `./get-wget-lua.sh` supposedly doesn't work due to differences in the `tar` that ships with FreeBSD. Another problem is the apparent absence of Lua 5.1 development headers. If you figure this out, please do let us know on IRC (irc.efnet.org #isoprey).

Troubleshooting
=========================

Broken? These are some of the possible solutions:

### wget-lua was not successfully built

If you get errors about `wget.pod` or something similar, the documentation failed to compile - wget-lua, however, compiled fine. Try this:

    cd get-wget-lua.tmp
    mv src/wget ../wget-lua
    cd ..
    
The `get-wget-lua.tmp` name may be inaccurate. If you have a folder with a similar but different name, use that instead and please let us know on IRC what folder name you had!

Optionally, if you know what you're doing, you may want to use wgetpod.patch.
    
### ImportError: No module named seesaw

If you're sure that you followed the steps to install `seesaw`, permissions on your module directory may be set incorrectly. Try the following:

    chmod o+rX -R /usr/local/lib/python2.7/dist-packages

### Other problems

Have an issue not listed here? Join us on IRC and ask! We can be found at irc.efnet.org #isoprey.
