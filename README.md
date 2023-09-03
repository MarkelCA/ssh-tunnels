# What is this script for?
One of the typical scenarios where ``ssht`` is helpful is depicted in the figure below. User may need to connect a port of a remote database (i.e. 3306) where only SSH port (usually port 22) is reachable.

```
    ----------------------------------------------------------------------

                                |
    -------------+              |    +------------+
        LOCAL    |              |    |  REMOTE    | :22 SSH
        CLIENT   | <== SSH ========> |  DATABASE  | :3306 database
    -------------+              |    +------------+
                                |
                             FIREWALL (only port 22 is open)

    ----------------------------------------------------------------------
```
**Fig1**: How to connect to a service blocked by a firewall through SSH tunnel.

If allowed by the SSH server, it is also possible to reach a private database (from the perspective of ``REMOTE SERVER``) not directly visible from the outside (``LOCAL CLIENT``'s perspective). 
```

    ----------------------------------------------------------------------

                                |
    -------------+              |    +----------+               +-----------+
        LOCAL    |              |    |  REMOTE  | :22 SSH       | PRIVATE   |
        CLIENT   | <== SSH ========> |  SERVER  | <== local ==> | DATABASE  | :3306 database
    -------------+              |    +----------+               +-----------+
                                |
                             FIREWALL (only port 22 is open)

    ----------------------------------------------------------------------
```
**Fig2**: How to connect to ``PRIVATE DATABASE`` through SSH tunnel.

*Credits on this section to [pahaz/sshtunnel](https://github.com/pahaz/sshtunnel)*
# Installation
## Dependencies
- [yq](https://github.com/mikefarah/yq) (YAML Query)
- [openssh](https://www.openssh.com): The `ssh` command (Should be available in most Linux distributions).

## Install
```bash
sudo wget https://github.com/MarkelCA/ssh-tunnels/releases/download/latest/ssht -O /usr/bin/ssht \
    && sudo chmod +x /usr/bin/ssht
```

# Configuration
The default configuration file is read from `~/.config/ssht/ssht.yml`. However, you can specify other files using the `-f` flag for every command.

To create your configuration copy the example yaml:
```bash
mkdir -p ~/.config/ssht/
cp ./ssht.example.yml ~/.config/ssht/ssht.yml
```
Now modify the file to add your own tunnel configurations.

## Example
Let's break down the example from `ssht.example.yml`.

```yml
tunnels:
  remote_database:
    host_destination: 127.0.0.1
    port_destination: 3306
    port_forward: 3333
    host_server: remote-server.com
    user_server: myuser
    ssh_key_path:  ~/.ssh/remote_server_key

  private_database:
    host_destination: private-database.com
    port_destination: 3306
    port_forward: 3335
    host_server: remote-server.com
    user_server: myuser
    # (missing ssh_key_path) -> In this case it will pick the ssh key from the ~/.ssh/config file
```

This example config file complements the [first section's explanation](https://github.com/MarkelCA/ssh-tunnels/tree/master#what-is-this-script-for). The `remote_database` would represent the first picture, where the `host_destination` and the `host_server` is the same, while the `private_database` example does likewise with the second picture, where the database lies in the same network but not the same machine as the `host_server`.

If you're familiar with the openssh's tunnel management the params from the yaml file will be transformed to this command:
`ssh -N -L <port_forward>:<host_destination>:<port_destination> <user_server>@<host_server> -f -i <ssh_key_path>`

Examples:
`ssh -N -L 3333:127.0.0.1:3306 myuser@remote-server.com -f -i ~/.ssh/remote_server_key`
`ssh -N -L 3335:private-database.com:3306 myuser@remote-server.com -f`

If no `ssh_key_path` if provided the `ssh`'s command `-f` option will be ommited and the command will be tried with the default key specified at the `~/.ssh/config` file.



# Run
You can type the help command to learn the usage.
```
➜  ~ ssht help
Manages SSH tunnels from a YAML configuration.

Usage:
ssht <command> [-f|--file <file>]

Commands:
    - open [query]    Opens ssh tunnels
    - close [query]   Closes ssh tunnels
    - status [query]  Tells if a tunnel is opened or closed.
    - show [query]    Shows an ssht.yml configuration
    - list            Lists the available tunnels in the configuration
    - help [command]  Shows the help description for a command

Options:
    - -f|--file   The configuration file. Default if none is
                  provided: ~/.config/ssht/ssht.yml

Help with specific command:
ssht help <command>

Example:
ssht help open
ssht help help

For more info visit: https://github.com/markelca/ssh-tunnels#example
```
