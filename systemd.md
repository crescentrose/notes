# systemd

systemd is the default init system on most Linux distributions. systemd is kinda like going to high school - no one ever has a good time with it, but it sure as hell beats _not_ having it.

## Services

### Create and edit services the easy way

`systemctl` provides a fun and interactive way to edit services with your `$EDITOR`. Use the `systemctl edit` command to never have to think about where to place service files, timers, mounts or whatever systemd comes up with ever again.

```bash
# override the default service file with specific custom instructions
sudo systemctl edit <service>

# edit the default service file
sudo systemctl edit --full <service>

# create a new service file and edit it
sudo systemctl edit --full --force <new-service>
```

