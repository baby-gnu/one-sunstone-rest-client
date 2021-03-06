# OpenNebula Sunstone REST API client

The `osc` command line permits to run `ssvncviewer` over the websocket
provided by OpenNebula websocket proxy.

# Installation

`osc` is self contained for the moment, you just need to clone this
repository and add it to `$PATH`.

## Dependencies

It requires the following dependencies:

* [gevent](http://www.gevent.org/)
* [BeautifulSoup4](http://www.crummy.com/software/BeautifulSoup)
* [websocket](https://github.com/liris/websocket-client)
* [Requests](http://python-requests.org)

## Compatibility

We now only support python3 since we use f-strings and remove
`urlparse` dependency.

# Usage

Just run the `osc` script, it will show you the list of your virtual
macines and ask you which action to run on the selected one.

You can provide the `--vm <ID>` to specify the ID of the virtual
machine.

Actually, this script only permit to start a `ssvncviewer` over
WebSocket, either by selecting it interactively or by using `--startvnc` command line option.

`osc` use
[one_auth](http://docs.opennebula.org/stable/administration/users_and_groups/manage_users.html#shell-environment)
to play Basic Authentication on Sunstone.

You can provide the `--auth` parameter to point to alternative
`one_auth` file.

The Sunstone URL is retrieve in the following order:

1. from command line parameter `--url`
2. from environment variable `ONE_SUNSTONE`

If your OpenNebula NoVNC is behind a reverse proxy, you will need to
pass options `--ws-port` to specify the port number and `--ws-path` to
specify the URL path.

By default, `--ws-port` is `29876` and `--ws-path` is `/` but if your
NoVNC is accessible on port `443` under the directory `/websockify`
(as [proposed in issue#2781](https://github.com/OpenNebula/one/issues/2781)),
you must use:

```
osc --url https://one.example.net --ws-port 443 --ws-path /websockify
```

# Limitations

Actually it can only run `ssvncviewer` on one virtual macine.

The goal is to have an interactive shell to successively and
concurrently run actions on virtual macines, for example, we can
imagine the following use case:

1. Start VNC client on virtual macine `X` => this spawn a new
   `ssvncviewer` and come back to the interactive shell
2. PowerOff virtual macine `Y` => this start the `POWEROFF` command on
   the virtual macine, come back to the interactive shell
3. Start VNC client on virtual macine `Z` => this spawn a new
   `ssvncviewer` and come back to the interactive shell
4. virtual macine `Y` is now `POWEROFF`, a message is displayed on the
   console
