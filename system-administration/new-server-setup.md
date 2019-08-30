# New server setup

## Setting up a new server checklist

* set root password
* set hostname
* enable firewall
* update skeleton files
* add unprivileged users with randomly generated passwords
* expire other users' passwords 
* configure SSH

### Change hostname \(systemctl\)

```bash
sudo hostnamectl set-hostname [new_hostname]
```

### Set up a better Bash prompt

Having an indicator of the server name should help users know which server they are on, so that they don't accidentally run the wrong command on a wrong server. Append this to `/etc/skel/.bashrc`:

```bash
# Ensure everyone knows the server they are on
export PS1="\[\e[30;42m\][\[\e[m\]\[\e[30;42m\]\h\[\e[m\]\[\e[30;42m\]]\[\e[m\] $PS1"
```

This will add a green tag with the server's hostname in front of the prompt. The users can disable it by editing their `.bashrc` if they so desire.

### Expiring a user's password

Forces a user to change their password on next login. They will need the current password, so keep that one safe. Use this to mail someone their initial password and then force them to change it on first login so that they can't blame you for keeping their initial password forever.

```bash
sudo passwd -e [username]
```

### Set up SSH authentication for a new user

```bash
# create a .ssh directory in their home dir
mkdir /home/$NEWUSER/.ssh
# paste their public key in the new dir
vi /home/$NEWUSER/.ssh/authorized_keys
# set appropriate permissions
chown -R $NEWUSER:$NEWUSER /home/$NEWUSER/.ssh
chmod 700 /home/$NEWUSER/.ssh
chmod 600 /home/$NEWUSER/.ssh/authorized_keys
```

### Harden access

Edit the following directives in the `/etc/ssh/sshd_config` file:

```text
PermitRootLogin no
PasswordAuthentication no
```

For additional security, you may disable root login completely \(`passwd -l root`\).

### Set up UFW

UFW is a simple iptables interface that manages rules in a very straightforward manner.

```bash
sudo ufw status
# => disabled by default
sudo ufw allow ssh
# => use service names
sudo ufw allow 80
# => use port numbers
sudo ufw default deny
# => all incoming connections not explicitly allowed will be dropped
sudo ufw enable
# => load the rules and enable them to be added on boot
sudo ufw reload
# => when changing rules
```

