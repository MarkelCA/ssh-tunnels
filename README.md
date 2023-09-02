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
You can check the config example from `ssht.example.yml`
```yml
# Format:
#   ssh -N -L <port_forward>:<host_destination>:<port_destination> <user_server>@<host_server> -f -i <ssh_key_path>
# Example:
#   ssh -N -L 3333:my-prod-database.com:3306 myuser@my-prod-server.com -f -i ~/.ssh/my_prod_ssh_key
tunnels:
  my_database.prod:
      host_destination: my-prod-database.com
      port_destination: 3306
      port_forward: 3333
      host_server: my-prod-server.com
      user_server: myuser
      ssh_key_path:  ~/.ssh/my_prod_ssh_key

  my_database.test:
    host_destination: my-test-database.com
    port_destination: 3306
    port_forward: 3334
    host_server: my-test-server.com
    user_server: myuser
    ssh_key_path:  ~/.ssh/my_test_ssh_key

  my_other_database:
      host_destination: my-other-database.com
      port_destination: 3306
      port_forward: 3335
      host_server: my-other-server.com
      user_server: myuser
      # (missing ssh_key_path) -> In this case it will pick the ssh key from the ~/.ssh/config file
```

# Run
You can type the help command to learn the usage.
```bash
➜  ~ ssht help
Manages SSH tunnels from a YAML configuration.

Usage:
ssht <command> [-f|--file <file>]

Commands:
    - open [query]    Opens ssh tunnels
    - close [query]   Closes ssh tunnels
    - show            Shows an ssht.yml configuration
    - list            Lists the available tunnels in the configuration

Options:
    - -f|--file   The configuration file. Default if none is
                  provided: ~/.config/ssht/ssht.yml

Help with specific command:
ssht help <command>

Example:
ssht help open
```
