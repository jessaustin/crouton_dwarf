Dwarf Fortress in Crouton
=========================

I don't always play [Dwarf Fortress][df], but when I do, I prefer a chromebook.
Here are the steps for getting [DF] working in glorious fullscreen X11, in
[Crouton][cr]. I am *not* setting up a window manager etc. for this, because
that would be overkill and overcomplicated. I *am* targeting 32 bit, because
that's what [DF] likes. It's not a problem to have a chroot especially for
[DF], because as the [crouton][cr] folks say, "Chroots are cheap!"

First of all, I assume you have your chromebook in developer mode. I won't help
you do this because brickage, but you might want to [start
here](//www.chromium.org/chromium-os/developer-information-for-chrome-os-devices).
After you've taken care of that, just `CTRL-ALT-T` into a terminal and download
[crouton][cr]:

```sh
chronos@localhost / $ cd
chronos@localhost ~ $ cd Downloads
chronos@localhost ~/Downloads $ wget -O crouton.sh http://goo.gl/fd3zc
```

Now put [crouton][cr] to work:

```sh
chronos@localhost ~/Downloads $ sudo sh crouton.sh -a i686 -r trusty -t x11 -n dwarf
```

This command builds a chroot for the `i386` 32-bit architecture, because that
avoids some fiddling with settings later. It targets `X11`, because that's what
we need to run [DF] outside a terminal. It builds the
[trusty](https://wiki.ubuntu.com/TrustyTahr) Ubuntu release, but you could
probably change that if you want. Also the chroot is named `dwarf`. This
command will happily download and build by itself for some time, but eventually
it will have some questions for you:

```sh

Please specify a username for the primary user: fortress
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully

Here's some tips:

Audio from the chroot will now be forwarded to CRAS (Chromium OS audio server),
through an ALSA plugin.

Future Chromium OS upgrades may break compatibility with the installed version
of CRAS. Should this happen, simply update your chroot.

You can flip through your running chroot desktops and Chromium OS by hitting
Ctrl+Alt+Shift+Back and Ctrl+Alt+Shift+Forward.

Unmounting /mnt/stateful_partition/crouton/chroots/dwarf...
Done! You can enter the chroot using enter-chroot.
```

At this point you're ready to enter your chroot.

```sh
chronos@localhost ~/Downloads $ sudo enter-chroot -n dwarf
Password: 
Entering /mnt/stateful_partition/crouton/chroots/dwarf...
(dwarf)fortress@localhost:~$ 
```

Now you need to install some libraries. `sudo` will ask for the password you
configured earlier with `crouton`. 

```sh
(dwarf)fortress@localhost:~$ sudo apt-get install -y libsdl-image1.2 libsdl-sound1.2 \
      libsdl-ttf2.0-0 libgtk2.0-0 libopenal1
[sudo] password for fortress: 
```

Finally we're ready to download [Dwarf Fortress][df]! The most recent version
as of when I'm writing this is **0.40.11**. Download the tarfile and then untar it:

```sh
(dwarf)fortress@localhost:~$ wget \
      http://www.bay12games.com/dwarves/df_40_11_linux.tar.bz2
(dwarf)fortress@localhost:~$ tar -xjf df_40_11_linux.tar.bz2
```

Now you need to set up some library links, because [DF] is kind of silly:

```sh
(dwarf)fortress@localhost:~$ cd df_linux/libs/
(dwarf)fortress@localhost:~/df_linux/libs$ ln -s \
      /usr/lib/i386-linux-gnu/libopenal.so.1 libopenal.so
(dwarf)fortress@localhost:~/df_linux/libs$ ln -s \
      /usr/lib/i386-linux-gnu/libsndfile.so.1 libsndfile.so
```

Now you want to edit the configuration slightly. Specifically edit the
`~/df_linux/data/init/init.txt` file so that the `WINDOWED` line looks like this:

```
  [WINDOWED:NO]
```

You'll probably want to come back and edit this file some more so that you can
use graphics and tilesets. For now, just run [DF] and watch that awesome intro:

```sh
(dwarf)fortress@localhost:~/df_linux/data/init$ cd
(dwarf)fortress@localhost:~$ startx ./df_linux/df
```

[df]: //www.bay12games.com/dwarves/ "Dwarf Fortress"
[cr]: //github.com/dnschneid/crouton "Crouton"
