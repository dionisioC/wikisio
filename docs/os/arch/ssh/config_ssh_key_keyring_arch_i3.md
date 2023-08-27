---
title: Configure ssh keys and Gnome keyring in arch and i3
date: "20230827"
author: Dionisio
---

First know that Gnome keyring is not going to be unlocked if there is an
autologin, so the `/etc/lightdm/lightdm.conf`
must have autologin-user commented or deleted

Install Gnome keyring and other needed dependencies

``` shell
sudo pacman -S gnome-keyring libsecret seahorse
```

Inside the `.zshenv` file

``` shell
if [ -n "$DESKTOP_SESSION" ]; then
    eval $(gnome-keyring-daemon --start)
    export SSH_AUTH_SOCK
fi
```

In the `~/.ssh/config`

``` text
Host *
    AddKeysToAgent yes
    IgnoreUnknown UseKeychain
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
```

Not sure if we have to run `seahorse` and create a password keyring called
`Login` in order to store the password or it is automatically created when
logged and the option of "Automatically unlock this key whenever I'm logged in"

Not sure if this is needed: `ssh-add ~/.ssh/id_ed25519`
