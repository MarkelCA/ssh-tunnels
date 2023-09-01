# Dependencies
[yq](https://github.com/mikefarah/yq)

# Configuration
Copy the `ssht.example.yml` to `ssht.yml` and configure it with your own variables

## Example
You can check the config example from `ssht.example.yml`
```yml
# Format:
#   ssh -N -L <port_forward>:<host_destination>:<port_destination> <user_server>@<host_server> -f -i <ssh_key_path>
# Example:
#   ssh -N -L 3333:my-database.com:3306 myuser@my-server.com -f -i ~/.ssh/my_ssh_key
my_database:
  prod:
    host_destination: my-database.com
    port_destination: 3306
    port_forward: 3333
    host_server: my-server.com
    user_server: myuser
    ssh_key_path:  ~/.ssh/my_ssh_key

```

# Run
```bash
./ssht my_database.prod ./ssht.example.yml
```
Output:
```ssh
➜  ssh-tunnels git:(master) ./ssht my_database.prod ./ssht.example.yml
Running: ssh -N -L 3333:my-database.com:3306 myuser@my-server.com -f -i ~/.ssh/my_ssh_key
Warning: Identity file /home/markel/.ssh/my_ssh_key not accessible: No such file or directory.
```
