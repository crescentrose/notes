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

### Using systemd to automatically restart Puma server on boot

View [this GitHub link](https://github.com/puma/puma/blob/master/docs/systemd.md#alternative-forking-configuration) for more details on achieving this.

If you are using RVM or rbenv, don't forget to add the necessary environment variables.

```text
# Substitute {username}, {application} and {ruby_version} to get your
# full service file
# e.g. (in Vi): %s/{ruby_version}/2.6.3/g

[Unit]
Description=Puma HTTP server
After=network.target

[Service]
# Background process configuration (use with --daemon in ExecStart)
Type=forking

# Preferably configure a non-privileged user
User={username}

# The path to the puma application root
WorkingDirectory=/srv/{application}/current

# The command to start Puma
ExecStart=/srv/{application}/current/bin/bundle exec puma -C /srv/{application}/shared/puma.rb --daemon

# The command to stop Puma
ExecStop=/srv/{application}/current/bin/bundle exec pumactl -S /srv/{application}/shared/tmp/pids/puma.state stop

# Should systemd restart puma?
Restart=on-failure

# `puma_ctl restart` wouldn't work without this.
# See the original config file for details.
RemainAfterExit=yes

# Include these environment variables if you are using RVM - don't forget
# to update with proper paths depending on your Ruby version!
Environment=GEM_HOME=/srv/{application}/.rvm/gems/ruby-{ruby_version}
Environment=PATH=/srv/{application}/.rvm/gems/ruby{ruby_version}/bin:/srv/{application}/.rvm/gems/ruby-{ruby_version}@global/bin:/srv/{application}/.rvm/rubies/ruby-{ruby_version}/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/srv/{application}/.rvm/bin

# Choose one or the other (rvm or rbenv) - using both will muck it up.

# Include these environment variables if you are using rbenv - no need to
# specify the proper version because rbenv is magic!
Environment=RBENV_ROOT=/srv/{application}/.rbenv
Environment=PATH=/srv/{application}/.rbenv/shims:/srv/{application}/.rbenv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin

[Install]
WantedBy=multi-user.target
```



