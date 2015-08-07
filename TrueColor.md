Colors in terminal
==================
It's a common confusion about terminal colors... Actually we have this:
* plain ascii
* ansi escape codes (16 color codes with bold/italic and background)
* 256 color palette (216 colors + 16 gray + ansi) (colors are 24bit)
* 24bit true color ("888" colors (aka 16 milion))

```
printf "\x1b[${bg};2;${red};${green};${blue}m\n"
```

The 256 color palete is configured at start, and it's a 666 cube of
colors, each of them defined as a 24bit (888 rgb) color.

This means that current support can only display 256 different colors
in the terminal, while truecolor means that you can display 16 milion
different colors at the same time.

Truecolor escape codes doesnt uses a color palete. It just specifies the
color itself.

Here's a test case:
```
printf "\x1b[38;2;255;100;0mTRUECOLOR\x1b[0m\n"
```
* or https://raw.githubusercontent.com/JohnMorales/dotfiles/master/colors/24-bit-color.sh
* or http://github.com/robertknight/konsole/tree/master/tests/color-spaces.pl
* or https://git.gnome.org/browse/vte/tree/perf/img.sh?h=vte-0-36

Keep in mind that it is possible to use both ';' and ':' as parameters delimiter.

According to Wikipedia[1], this is only supported by xterm and konsole.

[1] https://en.wikipedia.org/wiki/ANSI_color

Currently, there is no support for the 24-bit color descriptions in the terminfo/termcap database and utilites.
See the discussion thread here: https://lists.gnu.org/archive/html/bug-ncurses/2013-10/msg00007.html

Here are terminals discussions:
==============================

Now **supporting** truecolor
----------------------------

* [st](http://st.suckless.org/) (from suckless) [delimeter: semicolon] -  http://lists.suckless.org/dev/1307/16688.html
* [konsole](http://kde.org/applications/system/konsole/) [delimeter: colon, semicolon] - https://bugs.kde.org/show_bug.cgi?id=107487
* [iterm2](http://www.iterm2.com/) [delimeter: colon, semicolon] - only in the nighties https://code.google.com/p/iterm2/issues/detail?id=218
* [qterminal](https://github.com/qterminal/qterminal) [delimeter: semicolon] - https://github.com/qterminal/qterminal/issues/78
* [Tera Term](http://en.sourceforge.jp/projects/ttssh2/) [delimeter: colon, semicolon] - **Windows platform**
* [ConEmu](https://github.com/Maximus5/ConEmu) [delimeter: semicolon] - **Windows platform**
* [FinalTerm](http://finalterm.org/) [delimeter: semicolon] - [abandoned](http://worldwidemann.com/finally-terminated/), iTerm2 [borrowing it's ideas and features](http://iterm2.com/shell_integration.html).
* [mintty](https://mintty.github.io/) [delimeter: semicolon] **Cygwin and MSYS/MSYS2** since commit https://github.com/mintty/mintty/commit/43f0ed8a46c6549cb9a3ea27abc057b5abe13bdb (2.0.1 release) - **Windows platform**
* all [libvte](http://ftp.gnome.org/pub/GNOME/sources/vte/) based terminals (since 0.36 version) [delimeter: colon, semilocon] -  https://bugzilla.gnome.org/show_bug.cgi?id=704449
    * **libvte**-based [Gnome Terminal](https://help.gnome.org/users/gnome-terminal/stable/)
    * **libvte**-based [sakura](http://www.pleyades.net/david/projects/sakura)
    * **libvte**-based [Terminator](http://gnometerminator.blogspot.com/p/introduction.html) - use [GTK+3](https://code.launchpad.net/~gnome-terminator/terminator/gtk3) version.
    * **libvte**-based [Lilyterm](http://lilyterm.luna.com.tw/) - since commit https://github.com/Tetralet/LilyTerm/commit/72536e7ba448ad9ef1126ce45fbde3a3407a271b
    * **libvte**-based [ROXTerm](http://roxterm.sourceforge.net/)
    * **libvte**-based [evilvte](http://www.calno.com/evilvte/) - no release yet, version from git https://github.com/caleb-/evilvte
    * **libvte**-based [Termit](https://github.com/nonstop/termit)
    * **libvte**-based [Termite](https://github.com/thestinger/termite)
    * **libvte**-based [Tilda](https://github.com/lanoxx/tilda)
    * **libvte**-based [tinyterm](https://code.google.com/p/tinyterm)
    * **libvte**-based [lxterminal](http://sourceforge.net/projects/lxde) - with **--enable-gtk3** configure flag.
    * **libvte**-based [mlterm](http://mlterm.sourceforge.net) - with **--with-gtk=3.0** configure flag

But there are bunch of libvte-based terminals for GTK2 so they are listed in the another section.

Parsing ANSI color sequences, but approximating them to 256 palette
-------------------------------------------------------------------

* xterm (though doing it wrong: "it uses nearest colour in RGB colour space, with a usualfalse assumption about orthogonal axes")

Note about colour differences: a) RGB axes are not orthogonal, so you cannot use sqrt(R^2+G^2+B^2) formula, b) for colour differences there is more correct (but much more complex) [CIEDE2000](http://en.wikipedia.org/wiki/Color_difference#CIEDE2000) formula (which may easily blow up performance if used blindly) [2].

[2] https://github.com/neovim/neovim/issues/793#issuecomment-48106948

**NOT supporting** truecolor
----------------------------

* [urxvt](http://software.schmorp.de/pkg/rxvt-unicode.html) (patched version [3] available) -  http://lists.schmorp.de/pipermail/rxvt-unicode/2013q3/001826.html 
* [Terminology](https://www.enlightenment.org/p.php?p=about/terminology) (E17) - https://phab.enlightenment.org/T746
* [mrxvt](https://sourceforge.net/projects/materm) (looks abandoned) - https://sourceforge.net/p/materm/feature-requests/41/
* [aterm](http://www.afterstep.org/aterm.php) (looks abandoned) - https://sourceforge.net/p/aterm/feature-requests/23/
* [fbcon](https://www.kernel.org/doc/Documentation/fb/fbcon.txt) (from linux kernel) - https://bugzilla.kernel.org/show_bug.cgi?id=79551
* FreeBSD console - https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=191652
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) (patched version [4] {xterm-like approximation to 256 colors} and [5] {real true colors} available) - **Windows platform**
* libvte and GTK2 - based:
     * **libvte**-based [GTKTerm2](http://gtkterm.feige.net/)
     * **libvte**-based [stjerm](https://github.com/stjerm/stjerm) - https://github.com/stjerm/stjerm/issues/39
     * **libvte**-based [xfce4-terminal](http://docs.xfce.org/apps/terminal/start) - will be solved automatically since Xfce [slowly migrating](https://www.linuxliteos.com/forums/off-topic/ikey-porting-xfce-to-gtk3/) to the GTK+3

[3] You can download patched version here https://github.com/spudowiar/rxvt-unicode/tree/24bit

[4] You can download patched version here https://github.com/rdebath/PuTTY

[5] You can download patched version here https://github.com/halcy/PuTTY

Terminal multiplexers
---------------------

* [tmux](http://tmux.github.io/) (have workaround[6] and patch[7]) - https://github.com/tmux/tmux/issues/34 Older: https://sourceforge.net/p/tmux/tickets/140/ and https://www.mail-archive.com/tmux-users@lists.sourceforge.net/msg04720.html
* screen

[6] Currently you can use tmux_escape option as a workaround if you want true color in shell run under tmux. No true color in tmux statusline though.

[7] You can see the patch here https://gist.github.com/JohnMorales/0579990993f6dec19e83

Here are another console programs discussions:
============================================

Supporting True Color:

* irssi - https://github.com/irssi/irssi/pull/48
* neovim - https://github.com/neovim/neovim/commit/8dd415e887923f99ab5daaeba9f0303e173dd1aa
* elinks - http://repo.or.cz/w/elinks.git/blob/HEAD:/configure.in#l1410 (./configure --enable-true-color)

Not supporting True Color:

* mutt - http://dev.mutt.org/trac/ticket/3674
* mc - http://www.midnight-commander.org/ticket/3145#comment:1 - demo patches attached
* s-lang library - http://lists.jedsoft.org/lists/slang-users/2014/0000002.html
* ncurses library - https://lists.gnu.org/archive/html/bug-ncurses/2013-10/msg00007.html
* termbox library - https://github.com/nsf/termbox/issues/37
* mcabber - https://bitbucket.org/McKael/mcabber-crew/issue/126/support-for-true-color-16-millions-colors
* emacs - http://emacs.1067599.n5.nabble.com/RFC-Add-tty-True-Color-support-td299962.html and http://emacs.1067599.n5.nabble.com/bug-20243-True-color-terminal-support-tc354040.html
* vim - https://bitbucket.org/ZyX_I/vim/commits/branch/24-bit-xterm
* tig - https://github.com/jonas/tig/issues/227
