
source:  http://tekguru.wordpress.com/2013/08/19/howto-bring-your-dotfiles-under-version-control/



HOWTO: Bring Your Dotfiles Under Version Control
Posted on August 19, 2013 by hypercube	

For the uninitiated, dotfiles are configuration files for various applications in Linux (such as Vim, tmux etc.). Unless you work and play on one single system all your life, chances are that you’ll want to “move” your work (or play) environment from system to system in as painless a way as possible.

To make matters worse (or better, depends on how you look at it), dotfiles change. And when you add that neat new Vim key binding to your .vimrc, you expect the .vimrc’s in all your other machines to get updated as well. You can only copy files around for so long before you give up and Google for “how to bring dotfiles under version control” and end up reading this post.

Anyway, if you haven’t guessed already, Version Control Systems (such as GIT, SVN etc.) are an excellent solution to this problem. The only problem is: dotfiles are scattered around all over the place in your home directory. How do you bring them all under one roof so that GIT (or SVN) can work its magic? You definitely don’t want to bring your entire home directory under version control just for a few dotfiles.

Note: There are a number of possible solutions to this (I’ll go out on a limb and say that that Google search pointed to more sites than just mine)  – and the one I describe in this post is simply one that has worked out well for me over the years. YMMV.

So, without blabbering on further about why you need this, I’ll get right to it.

Step 1: Set up a GitHub Repository

The first step is to create a repository for your dotfiles in GitHub (if you don’t have a GitHub account yet, now’s a good time to create one).

Note: You can use any other Version Control System for this HOWTO as well, if you so please. Just change the Git commands to your system’s equivalents.

Let’s call the new repo, unimaginatively, ‘dotfiles‘. You can choose to create the README file when creating the new repo, it’s up to you. Doesn’t matter for this HOWTO.

While you’re at it, it’s also a good idea to set up access to the account using SSH keys from your different machines. A good tutorial is available here.

Step 2: Move dotfiles to a Common Folder

We’ll be storing all your dotfiles in one common subdirectory. So let’s set that up.

    Create a directory ‘dotfiles’ in your home directory
    $ cd ~
    $ mkdir dotfiles
    Move your dotfiles to this directory like so:
    $ cd ~
    $ mv .vimrc dotfiles/ [Note the slash at the end]
    Similarly, move all your other dotfiles to dotfiles/

Now, let’s move all these new files in dotfiles/ under version control:

$ cd ~/dotfiles
$ git init
$ git commit -a

The last command (git commit -a) will open up an editor window. Type your initial commit message (something like “Initial commit – adding dotfiles”), save and exit the editor. All your dotfiles are now staged and ready to be pushed to the repository like so:

$ git remote add origin https://github.com/username/dotfiles.git
$ git push origin master

That’s it! Your dotfiles are now safely stored in version control.

Step 4: Write and Check In Setup Scripts

All the dotfiles are now in version control. They’re no longer in your home directory anymore though. So is all that we’ve done an exercise in stupidity? Of course not!

In this step, we’ll write scripts that create symlinks of your dotfiles (which now reside in ~/dotfiles) and place them in the correct locations within your home directory. So let’s do that.

$ cd ~/dotfiles
$ vim setup.sh

In the editor, type out the following:

#!/bin/bash

cd ~/dotfiles
ln -s .vimrc ~/.vimrc
ln -s .tmux.conf ~/.tmux.conf

You’ll need to put a separate ‘ln’ here command for each dotfile that you put under version control.

Once you’ve filled in the setup.sh file, let’s check that into version control as well:

$ cd ~/dotfiles
$ git add setup.sh
$ git commit -m "Adding setup script"
$ git push

Don’t forget to execute setup.sh on your own system!

$ cd ~/dotfiles
$ ./setup.sh

Step 5: Migrating to New Systems

Now let’s talk about the main reason why we went through all this hassle: migration to new systems and keeping versions on different machines in sync.

Moving to a new system: On a new system, all we need to do is:

$ cd ~
$ git clone git@github.com:username/dotfiles.git
$ cd dotfiles
$ ./setup.sh

And we’re done. All the relevant symlinks will be created and life will be good.

Updating dotfiles: Whenever you make a change to any dotfiles, simply do:

$ git add [name-of-dotfile]
$ git commit -m "Commit Message"
$ git push

And an updated version of your dotfile(s) will be stored on GitHub. On the other systems, you do:

$ cd ~/dotfiles
$ git pull

And the updated dotfiles will be available immediately on your other systems.

If you add a config file, you’ll have to:

$ cd ~/dotfiles
$ git add [name-of-dotfile]
$ git commit -m "Commit Message"
$ git push

And on the other systems,

$ cd ~/dotfiles
$ git pull
$ ./setup.sh

And you can get back to work!

Hope this little HOWTO helped. If you’ve got any comments/feedback, feel free to post them in the comments section!



