#  Development Utils 

This tutorial introduces a series of utilities that are designed to make developing on remote servers a more pleasant experience. To fully enjoy the benefits, please use iTerm2 if you have MacOS or Cygwin if you are a Windows user. Linux users already have all they need.

Why should I need these development utils? I hear you say. Well, have you ever left your laptop running all night long just because training your model took too long?  Well, by the end of this tutorial hopefully you'll never have to do that again, I promise.

## Foreword

> *Don't Panic*

Yes, I'm blatantly stealing the famous cover words from the [The Hitchhiker's Guide to the Galaxy](https://en.wikipedia.org/wiki/The_Hitchhiker%27s_Guide_to_the_Galaxy) to start off this tutorial. I'm doing this since I want to (), as we will be using the command line throughout this tutorial. Don't forget to grab your [towel](https://en.wikipedia.org/wiki/Towel_Day) and let's get started already.  

## tmux

The first uitil I'll be introducing is `tmux`, a terminal multiplexer. It allow us to run multiple terminal sessions inside a single terminal window.  

`tmux` allows us to completely transform our boring terminals intomuch more friendly, informative and full-fledged tools. Besides being easily configurable, one of the features that `tmux` brings into our world is that it wil let us `detach` from a terminal with programs or scripts running  on it, while keeping the session alive in the background.  Sounds good? Let's try it out.

If you have admin privileges, in Ubuntu you can install `tmux` using the `apt` package manager by simply running `apt install tmux`. In case you do not have sudo privileges, refer to my [no_sudo_install_tmux script](https://gist.github.com/epochx/2abc52902ce99c2a3c8907ccad927581) wich should get you version 2.5. If you are using MacOS, you can use the Homebrew package manager, and run `brew install tmux`. To install `tmux` on Cygwin, you can use the Cygwin installer directly.

 I would strongly advice anyone to use at least `tmux` version 2.1, otherwise some of the utils  may not work properly, or at all. You can check the version you have installed by running `tmux -V`.

###  Using `tmux`

Let's start by opening our terminal and running the `tmux` command with no additional parameters. Everytime you do this , a new unnamed `session` will be created. As long as your machine is running,  `sessions` will remain alive in the background, even if you close the terminal window where they were spawned. 

- You can `attach` or `detach` from any running session.  If you `detach` from a session and later `attach` to it, you will pick it up exactly where you left it.

- To list existing sessions anytime, call `tmux ls`.

- To attach to an existing session use `tmux a -t <session-name>`. When starting an unnamed session, as we did earlier the plain`tmux` command,  `tmux` will assign the new sessino a default name with a numbers starting from 0. Try opening a new terminal window and running `tmux a -t 0`.

- To `detach` from a session, simply run `tmux detach` 

### The `.tmux.conf` config file

OK, `tmux` is cool, but it is certainly not so cool to learn all those additional little commands to move around. To make this easier, I've created a series of shortcuts that are summarized in the `.tmux.conf` file, a plain text file storing the settings for our `tmux` . Download the [config  file](https://gist.githubusercontent.com/epochx/bb980b78cfafde90457d7202ab90502a/raw/7d6083f9c18a5b57fd10c2f871df6f4a401b1a1d/.mytmux.conf) into your home directory, usually `/home/<username>` in linux distros, `/Users/<username>` in MacOS and `\Users\<username>` in Windows\. If you already have a tmux config file there, I'd recommend you keep a backup of that one. This file introduces a number of shortcuts and other features that will hopfeully make your life easier when it comes to using `tmux`.  

- Open a terminal window, go to your home directory and run `mv ~/.mytmux.conf ~/.tmux.conf `

- After this,  start `tmux` and run `tmux source ~/.tmux.config` to load the settings. 


At this point you've probably noticed a super nice status bar in the lower part of the terminal,  showing some information about the current status of the `tmux` and yout machine machine.

Let's dive in. It turns out that each tmux `session` is divided into `windows`, which you can roughly think of as browser tabs). `windows` can further be divided into `panes`. To create and manipulate these divisions,  I've added  the following shortcuts.

| Shortcut              | Action                                 |
| --------------------- | -------------------------------------- |
| ctrl + F2             | Create a new window                    |
| ctrl + F3,  ctrl + F4 | Move to left, right window             |
| ctrl + F6             | Detach from current session            |
| ctrl + F8             | Rename current session                 |
| ctrl + \              | Split current window/pane vertically   |
| ctrl + -              | Split current window/pane horizontally |

To make navigation though  `windows` and `panes` more natural, `tmux` features mouse support for scrolling, `window` selection, `pane` resizing and for copy-pasting content. The config file includes settings to enable all these features. To make sure mouse support works in Windows with Cygwin, open the MinTTY options dialog (right click on the titlebar) and go to the "Mouse" category to ensure that "Default click target" is set to application. For MacOs, I advice not to use the default Terminal application, since neither F-key presses nor mouse actions are correctly handled.

Arguably, the only drawback of running `tmux` is that it makes copying content from the terminal a little less straightforward than usual. In order to do so, you have to hold shift while selecting the content you want to copy.  


## SSH 

SSH, or Secure Shell, is a remote administration protocol that allows users to control and  modify remote machines over the Internet. The service was created as a secure replacement for older unencrypted protocols to ensure that all communication to and from the remote machine happens in an encrypted manner. 

Before start using SSH, we need to create credentials that make connections secure. If you haven't created your own keys, run the command `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` to create them. The resulting credentials will be stored by default on the `/home/<username>/.ssh` folder.

### Using SSH

The SSH command consists of mainly 3 distinct parts:  `ssh <user>@<host>`. This command tells your system that you want to open an encrypted connection using account `<user>` , to a machine located in the `<host>` address. This address should be given in the form of an IP or a domain name (DNS).  

There are mainly two ways in which you can authenticate your connection. The availability of each authentication method will depend on the SSH server settings on the remote machine. The not-so-safe method is to simply use a password, which will be requested after you run the `ssh` command. For example, try running `ssh guest@nlp-tutorial.duckdns.org`. The safest and generally recommended method is for the server to only let certain users connect, based on their credentials.

Now, before we move on into more interesting stuff, there is one last important trick to talk about. Open the file `/home/.ssh/config` with your favorite text editor. We'll proceed to create our first alias for an SSH connection, by adding the following content at the end of the file.

```
Host nlp-tutorial 
user guest
HostName nlp-tutorial.duckdns.org
```

Save the file, open a terminal, and just write `ssh tutorial` to directly connect to the remote machine.  Congratulations, you've created your first SSH alias. There are tons of other ().

### SSH Port Forwarding A.K.A. SSH tunneling A.K.A. How I learned to stop worrying and Love SSH

Until now we know that SSH provides us with a neat way to connect to remote machines. But what if I told you that this is just the beginning? 

Since SSH is a communication protocol it can be used to wrap some other (potentially not protected) protocol and make it secure. This combo is used for countless of applications including secure file transfer and  firewall by-passing.

A special use case for us in this tutorial is to provide us with a secure connection to a Jupyter Notebook running in some other machine. First, let's give Jupyter a close look to understand how it works.  When you start the Jupyter Notebook Server on a machine, with the `jupyter notebook` command, what you are doing is basically starting a very specialized Web server that by default is listening for connections *only* comming from your machine at port 8888. This means that if you open your browser and go to the address `localhost:8888`, the server i.e. your machine will respond.  Your browser will then render the notebooks the so we can work some Jupyter magic. Although you can actually configure Jupyter to accept connections coming from any IP adddress -not just your local machine- this is rather insecure and therefore not recommended. Instead, let's SSH it! 

Since creating an SSH tunnels may be troublesome simetimes, I've created a small script to make this process easier. Besides having SSH aliases for your target machine, the only assumption for this hack to work is that the Jupyter Notebook Server is running on port 9999 which you can easily achieve by runnning `jupyter notebook --port 9999`. We will map this port in the remote machine to our local 8080 port, so the connection does not collide a local Jupyter instance that by default may be running in port 8888. 

- Connect to your server, and launch jupyter with the command `jupyter notebook --port 9999` 
- use `start-remote-notebook <alias>` to connect to the server and point it to your local 8080 port. You can now open your browser, go to [http://localhost:8080] and start working remotely.
- After you are finished working, you may use `kill-remote-notebook <alias>` to close the tunnel. Remember that the Jupyter Notebook server in your remote machine will stay open. If you keep it that way, all you have to do to reconnect is to use  the `start-remote-notebook` command again, and voilà.
- As a bonus feature, you can point your tunnel to a different local port by adding the port parameter after the server name `start-remote-notebook <alias> <port>`.

## Monitoring GPU usage and choosing between multiple GPUs

When developing or sharing a GPU machine, monitoring the GPU usage is one key aspect for development. To help with this process, I created a slightly modified version of the *gpustat* script, which offers features similar to *htop*, but focusing on GPU usage.
- paste the `gpustat.py` file in your home foder and call `gpustat` to launch a gpu monitor which inclides process id and user name identification.

On multiple GPU machines when using some DL libraries ---I'm looking at you, Tensorflow---  it is a good idea to have finer control of the GPUs that will be available when running some code.  I've created a few bash functions to do that.
- You may use the command `get-visible-gpus` to obtain a list of the GPU devices that are visible to the .
- Use `set-visible-gpus `
- Use `get-total-gpus` to see the number of total GPUs available in the system, regardless of their visibility in the current session.
- Finally, use `reset-visible-gpus` to make all GPUs visible in the current session again.

## Closing words: on the usefulness of dotfiles

Talk about dotfiles.

