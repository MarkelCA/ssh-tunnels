# Installation
## Dependencies
- [yq](https://github.com/mikefarah/yq) (YAML Query)
- [openssh](https://www.openssh.com) (The `ssh` command)

## Install

```bash
wget https://raw.githubusercontent.com/MarkelCA/ssh-tunnels/master/ssht \
    && sudo chmod +x ./ssht \
    && sudo mv ./ssht /usr/bin/
```

# Configuration
Copy the `ssht.example.yml` to `ssht.yml` and configure it with your own variables

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
```bash
./ssht open my_database.prod ./ssht.example.yml
./ssht open my_other_database # This will pick the ssh key from the ~/.ssh/config file
./ssht close my_other_database 
```
Output:
```ssh
➜  ssh-tunnels git:(master) ./ssht my_database.prod ./ssht.example.yml
Running: ssh -N -L 3333:my-database.com:3306 myuser@my-server.com -f -i ~/.ssh/my_ssh_key
Warning: Identity file /home/markel/.ssh/my_ssh_key not accessible: No such file or directory.
```
