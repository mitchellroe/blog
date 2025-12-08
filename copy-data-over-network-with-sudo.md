# Copy data over the network with sudo

## Situation

- You have two MariaDB servers, `src-server` and `dest-server`.
- You can SSH from one box to another, but only as an unprivileged user (i.e.
  not as root).
- You have full sudo privileges on both boxes.
- You want to copy `/srv/big-directory` from `src-server` to `dest-server`, but
  it’s only readable by root.

You have full sudo access on both boxes, so you should be able to copy the
files over, right?

## Problem

- As soon as you run sudo, you’re now acting as root, and root can’t SSH between
  boxes, remember?
- You could change the directory permissions, but then you have to do it on
  both `src-server` and `dest-server` (i.e. it’s messy). Also, this approach
  only works if you **can** safely change the permissions (e.g. you've got
  other processes that use `/srv`, so you don't want to change the ownership of
  `/srv`).

## Solution

### Pre-requisites

1. You have a running ssh-agent on your workstation.
2. The ssh-agent has your keys already added to it.
3. You are forwarding your SSH agent to the remote machine.

To accomplish (2) and (3), add the following to `~/.ssh/config`:

```txt
AddKeysToAgent yes
ForwardAgent yes
```

### Procedure

#### Using tar (faster, but no progress feedback or partial transfers)

```txt
[unprivileged.user@src-server]:~$ cd /
[unprivileged.user@src-server]:/$ sudo tar -cf - srv/big-directory | \
> ssh unprivileged.user@dest-server 'sudo tar -xf - -C /'
```

Here’s a breakdown:

- `[unprivileged.user@src-server]:~$` You are logged in as an unprivileged
  user, not as root.
- `cd /` First, go to the root directory for the system
- `sudo tar -cf - srv/big-directory`
  - `sudo`  Run the command as root, since root has permissions to the files in
    `/srv/big-directory`
  - `tar`  Make a **t**ape **ar**chive
  - `-cf -`  **c**reate a tape archive (`-c`), and place it at the given
    **f**ile (`-f`), which, when passed `-`, means the "file" is stdout.
  - `srv/git-directory`  The tar should contain the following directory. Note
    the omission of the leading slash. There’s a reason we’re running the
    command in the root directory (`/`).
- `|`  Pipe the output of our `sudo tar` to another command
- `ssh unprivileged.user@dest-server 'sudo tar -xf - -C /'`
  - `ssh unprivileged.user@dest-server`  We’re connecting as our
    `unprivileged.user` to `dest-server`
  - `'sudo tar -xf - -C /'`  Run this command on `dest-server` instead of a
    login shell.
    - `-x` means extract, `-f` tells it what file to extract, and `-f -` means
      the "file" is stdin
    - `-C /` The extraction should take place starting in the root directory
      (`/`).

#### Using rsync (slower, but gives progress feedback and can resume after interruption)

```txt
[unprivileged.user@src-server]:~$ sudo SSH_AUTH_SOCK=${SSH_AUTH_SOCK} \
> rsync -avz --progress --rsync-path='sudo rsync' \
> /srv/big-directory/ unprivileged.user@dest-server:/srv/big-directory/
```

Here’s a breakdown:

- `[unprivileged.user@src-server]:~$` We are currently logged in as
  `unprivileged.user` on `src-server` (i.e. not as root).
- `sudo` Run the command with elevated privileges, so we can access the files
  on `src-server`
- `SSH_AUTH_SOCK=${SSH_AUTH_SOCK}` Set an environment variable `SSH_AUTH_SOCK`
  for our `sudo` process so it points to the same authentication socket that we
  are currently using as `unprivileged.user`. This will allow us to use all the
  same SSH keys which are already in our ssh-agent.
- `rsync` (obvious)
- `-avz` **a**rchive mode, **v**erbose mode, and compress (**z**)
- `--progress`  Show the progress of each file as it is transferred
- `--rsync-path='sudo rsync'`  This tells the remote side how to use `rsync`,
  i.e., tell `dest-server` to run `sudo rsync` instead of just plain-ol'
  `rsync`. This will ensure we have privileges on the remote end to place the
  files where we want.
- `/srv/mysql/visitors.33100/`  Use this directory as the source. Copy only the
  contents of the directory, not the directory itself (as shown by the trailing
  `/`).
- `unprivileged.user@dest-server:/srv/mysql/visitors:33100/`  Connect to
  `dest-server` as `unprivileged.user`, and place all the files into
  `/srv/mysql/visitors.33100/`.

## Troubleshooting

### Reconnecting to a nested tmux session

<https://werat.dev/blog/happy-ssh-agent-forwarding/>

The above guide is quite helpful if you use tmux on your own machine, then want
to run tmux on the remote as well. If you disconnect from a tmux session on a
remote server and reconnect later, your ssh agent won’t work right. Here’s the
tl;dr:

- Put this in `~/.ssh/rc`:

  ```sh
  if [ ! -S ~/.ssh/ssh_auth_sock ] && [ -S "${SSH_AUTH_SOCK}" ]; then
    ln -sf "${SSH_AUTH_SOCK}" ~/.ssh/ssh_auth_sock
  fi
  ```

- And put this in `~/.tmux.conf`:

  ```sh
  set-environment -g 'SSH_AUTH_SOCK' ~/.ssh/ssh_auth_sock
  ```
