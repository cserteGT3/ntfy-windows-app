# Ntfy Windows App

I was planning to write a small app, that runs in the background and gives notification balloons for [ntfy](ntfy.sh) notifications.
But I didn't start it, and found a "workaround" using systemd in WSL and [notifu](https://www.paralint.com/projects/notifu/).

Needed components:

- WSL
- systemd (in WSL)
- command line ntfy (in WSL)
- notifu (in Windows)

The following guide hopefully describes every step you need to take to create this setup.

## Install systemd in WSL

I don't want to give a guide on installing WSL and systemd.
Latter is a relatively new feature (as of writing), you can set it up following [this guide](https://devblogs.microsoft.com/commandline/systemd-support-is-now-available-in-wsl/).

## Install ntfy

Follow the guide on [this site](https://ntfy.sh/docs/install/#general-steps) in WSL (for Debian/Ubuntu/etc.).
We will use the `ntfy-client` part of the software, based on [this](https://ntfy.sh/docs/subscribe/cli/?h=systemd#using-the-systemd-service) guide, but I'll detail these steps later.

## Install notifu

1. Download the binaries from the project's site: <https://www.paralint.com/projects/notifu/>.
2. Extract the zip file wherever you file and add it to PATH.
3. `notifu.exe` will be available from WSL after restarting it. (Run `wsl --shutdown` from terminal, then start again by opening it.)

## Configure ntfy

You will need to edit two files:

- systemd config file
- ntfy client config file

For the systemd config guide, you can issue the `sudo systemctl edit ntfy-client` command, then `sudo systemctl daemon-reload` and `sudo systemctl restart ntfy-client`.
Latter two needs to be run every time you edit the config file.
You need to add the following line to the config file (or to `override.conf`):

```ini
[Service]
# for notifu
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/mnt/c/where_you_installed_notifu"
```

Then you only need to edit the ntfy client config file (`/etc/ntfy/client.yml`) as you wish:

```yml
default-host: https://ntfy.myhost.com
subscribe:
  - topic: https://ntfy.myhost.com/torrent
    command: 'notifu.exe /p "$NTFY_TITLE" /q /t none /m "$NTFY_MESSAGE" /d 4000'
```

You can read more on the configuration in the [docs](https://ntfy.sh/docs/subscribe/cli/?h=systemd#install-configure).
