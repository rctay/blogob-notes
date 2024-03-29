---
title: smartcards
date: 2019-02-03 18:52:41 +0800
tags: security
licence: Copyright © 2019 Ray. <a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons Attribution 4.0 International License" src="https://i.creativecommons.org/l/by/4.0/80x15.png" /></a>
---

# Getting started with private keys on smartcards

I found these guides to be useful when I was getting started:

- https://raymii.org/s/articles/Nitrokey_Start_Getting_started_guide.html

  Targets the particular hardware I'm using, the Nitrokey Start.

- https://www.gniibe.org/memo/software/gpg/keygen-25519.html

  Ed25519 keys are relatively new. The console logs, coupled with the author's comments, made this very useful.

- https://alexcabal.com/creating-the-perfect-gpg-keypair

  Covers the lifecycle of keys from generating, backing up, and revoking them (in the worst case scenario!).

- https://shankarkulumani.com/2019/03/gpg.html

  Useful usage examples for signing, encrypting, etc.

# setup on mac

- Use pinentry, a nice UI for pin entry. Otherwise the curses[^curses] password entry
  unexpectedly waits for password in some other terminal (namely the one you
  started gpg-agent in...)

  ```console
  $ brew install gpg pinentry-mac
  $ echo pinentry-program /usr/local/bin/pinentry-mac >> ~/.gnupg/gpg-agent.conf
  ```

[^curses]: <https://en.wikipedia.org/wiki/Curses_(programming_library)>

# usage

- first time on new computer:

  ```console
  $ gpg --recv-keys C0023FA0
  ```
- first run: start gpg-agent

  ```console
  $ eval $(gpg-agent --daemon --enable-ssh-support)
  ```
- check:

  ```console
  $ ssh-add -l
  ```

You can now profit and run your ssh operations.

- subsequent run, if in another terminal:

  ```console
  $ unset GPG_AGENT_INFO SSH_AGENT_PID SSH_AUTH_SOCK
  $ export SSH_AUTH_SOCK=~/.gnupg/S.gpg-agent.ssh
  ```
- if wonky,

  ```console
  $ gpgconf --kill gpg-agent
  ```

  ...then run instructions in 'first run'

Adapted from: <https://www.bootc.net/archives/2013/06/09/my-perfect-gnupg-ssh-agent-setup/>

# on Windows

- Setup gpg-agent:

  ```console
  $ echo enable-putty-support > $APPDATA/gnupg/gpg-agent.conf
  ```
- Start gpg-agent daemon

  ```console
  $ gpg --card-edit
  ...
  > quit
  ```
- Accept host key in PuTTY

  ```console
  $ plink git@github.com
  ```
- tell git to use plink:

  ```console
  GIT_SSH_COMMAND=plink git clone git@github.com:...
  ```

# using side-by-side with Krypton

Krypton sets up `~/.ssh/config`. We need to tell `git` to use `ssh` without that config file, via `-F`.

1. `GIT_SSH_COMMAND='ssh -F /dev/null' git clone git@github.com:...`
