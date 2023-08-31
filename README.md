# Dependencies
[yq](https://github.com/mikefarah/yq)

# Configuration
Copy the `sht.example.yml` to `sht.yml` and configure it with your own variables

## Example
You can check the config example from `sht.example.yml`
```yml
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
./sht my_database.prod ./sht.example.yml
```
Output:
```ssh
➜  ssh-tunnels git:(master) ./sht my_database.prod ./sht.example.yml
Running: ssh -N -L 3333:my-database.com:3306 myuser@my-server.com -f -i ~/.ssh/my_ssh_key
Warning: Identity file /home/markel/.ssh/my_ssh_key not accessible: No such file or directory.
```
