# Installation
## Dependencies
- [yq](https://github.com/mikefarah/yq) (YAML Query)
- [openssh](https://www.openssh.com): The `ssh` command (Should be available in most Linux distributions).

## Install

```bash
wget https://raw.githubusercontent.com/MarkelCA/ssh-tunnels/master/ssht \
    && sudo chmod +x ./ssht \
    && sudo mv ./ssht /usr/bin/
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
# Use cases
```
Usage scenarios
===============

One of the typical scenarios where ``ssht`` is helpful is depicted in the
figure below. User may need to connect a port of a remote server (i.e. 8080)
where only SSH port (usually port 22) is reachable.

    ----------------------------------------------------------------------

                                |
    -------------+              |    +----------+
        LOCAL    |              |    |  REMOTE  | :22 SSH
        CLIENT   | <== SSH ========> |  SERVER  | :8080 web service
    -------------+              |    +----------+
                                |
                             FIREWALL (only port 22 is open)

    ----------------------------------------------------------------------

**Fig1**: How to connect to a service blocked by a firewall through SSH tunnel.


If allowed by the SSH server, it is also possible to reach a private server
(from the perspective of ``REMOTE SERVER``) not directly visible from the
outside (``LOCAL CLIENT``'s perspective). 

    ----------------------------------------------------------------------

                                |
    -------------+              |    +----------+               +---------
        LOCAL    |              |    |  REMOTE  |               | PRIVATE
        CLIENT   | <== SSH ========> |  SERVER  | <== local ==> | SERVER
    -------------+              |    +----------+               +---------
                                |
                             FIREWALL (only port 22 is open)

    ----------------------------------------------------------------------

**Fig2**: How to connect to ``PRIVATE SERVER`` through SSH tunnel.
```
*Credits on this section to [pahaz/sshtunnel](https://github.com/pahaz/sshtunnel)*
# Run
This commands are written for the above example configuration file (`ssht.example.yml`)
```bash
./ssht open my_database.prod -f ./ssht.example.yml
./ssht open my_other_database
./ssht close my_other_database 
```
Output:
```ssh
➜  ssh-tunnels git:(master) ./ssht my_database.prod ./ssht.example.yml
Running: ssh -N -L 3333:my-database.com:3306 myuser@my-server.com -f -i ~/.ssh/my_ssh_key
Warning: Identity file /home/markel/.ssh/my_ssh_key not accessible: No such file or directory.
```
