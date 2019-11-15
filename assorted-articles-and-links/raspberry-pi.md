---
description: >-
  Everything here applies only to the Raspberry Pi 3 B+. While it could apply
  (and probably does) to other versions as well, it has not been tested.
---

# Raspberry Pi

A spectacular little toy that can simultaneously do more than you think and less than you want it to.

The default flavour of Linux that comes with the Pi is [Raspbian](https://www.raspberrypi.org/downloads/raspbian/). If you're using the Pi for anything other than playing around, you'll probably want to set up the Raspbian Lite image that is basically the current Debian sans desktop environment. It's a fairly minimal image, but that's realistically the most you want to saddle the little Pi with. After all, surely you're not going to [run a Minecraft server as root on it](https://www.makeuseof.com/tag/setup-minecraft-server-raspberry-pi/)... right?

## Setting up the Pi

Rough notes for the kind of people who've done this kind of thing before.

1. Install Raspbian Lite using the built-in recovery tool. You'll need a monitor or a TV that has an HDMI input and a wired keyboard. Ensure an accessible Internet connection, and an SD card with a fair amount of storage \(SD cards are cheap these days so I suggest at least 32GB\).
2. After the installation finishes, reboot and log in as the Pi default user: `pi`/`raspberry`.
3. Immediately change the default user password to something that's not `raspberry`. You'll be starting the SSH server in the next step, so this is a great way to not get pwned immediately.
4. Start the SSH server \(you didn't see that coming, did ya?\). Using `sudo systemctl start ssh` followed by `sudo systemctl enable ssh` will do.
5. Before you unplug your Pi from your makeshift setup, you will probably want to ensure a static IP address so you can connect to it. Edit the `/etc/dhcpcd.conf` file and uncomment the example static IP configuration block, making changes as necessary.

{% code title="/etc/dhcpcd.conf" %}
```text
interface eth0
static ip_address=192.168.5.20/24
static routers=192.168.5.1
static domain_name_servers=192.168.5.1 1.1.1.1 1.0.0.1
```
{% endcode %}

Save and shut down your Pi, and move it in your desired location. I like to keep mine slightly out of sight.

Once you've reconnected your Pi, you should be able to ssh into it as the default `pi` user. Use your new powers to create your user and add it to the sudo group, and ssh as your new user, and do some extra setup.

```bash
# on the Pi
# create your user
sudo adduser <yourusername>
# let your user perform root commands
sudo adduser <yourusername> sudo
# log out
exit

# on your computer
ssh-copy-id <yourusername>@<yourpihostname>
ssh <yourusername>@<yourpihostname>

# on the Pi again
# disable the Pi user for a little bit more of extra security
sudo usermod -L pi
# disable wifi in case you don't need it (be nice to your router!)
sudo ip link set dev wlan0 down
```

You should now have a minimal, yet functional installation that you can then customize to your heart's content.

## Dynamic DNS

If you are like me you are probably running the Pi on a residential connection that gets a different IP every day. However, that doesn't mean you're stuck with an insecure, HTTP-only, IP+port-only access to your Raspberry. If you have a domain name, you can use [ddclient](http://ddclient.sourceforge.net) as a dynamic DNS client, automatically changing the IP to which your domain name points as it changes.

You probably shouldn't be hosting high traffic websites on your residential connection - there will be some downtime in DNS resolving, and your ISP probably won't be happy 🙃. You might also have to ask your ISP to remove you from NAT so that you don't share your IP with 10 other people.

My domain reseller of choice is Namecheap, so the rest of this tutorial will be based on them.

```bash
# install ddclient
# ignore the questions asked by the installer
sudo apt install ddclient
# edit the ddclient config file
sudo vim /etc/ddclient.conf
# edit the defaults file to run ddclient as daemon so that 
# you can run it as a service
# change `run_ipup` to false and `run_daemon` to true
sudo vim /etc/default/ddclient
# restart ddclient to pick up on the new config
# don't forget to enable it too
sudo systemctl start ddclient
sudo systemctl enable ddclient
```

Example Namecheap config, if you want to use a wildcard DNS entry so that all subdomains are redirected to the same IP:

```text
use=web, web=dynamicdns.park-your-domain.com/getip
protocol=namecheap 
server=dynamicdns.park-your-domain.com 
login=<your domain>
password=<password here>
*

```

## Docker

Docker works as expected on the Pi , and you can install it with the familiar incantation:

```bash
curl -fsSL get.docker.com -o get-docker.sh && sh get-docker.sh
```

The only caveat is that docker-compose offers no pre-built binaries for the ARM architecUture. To alleviate that, you will need to install it via `pip` like a peasant:

```bash
sudo apt update
sudo apt install -y python python-pip libffi-dev python-backports.ssl-match-hostname
sudo pip install docker-compose
```

The [With Blue Ink blog](https://withblue.ink/2019/07/13/yes-you-can-run-docker-on-raspbian.html) has more details \(and that's where the 3 lines above were yanked from\), except that it's wrong about `pip install docker-compose`taking a while - it takes more than a while...

## Pi-hole

[Pi-hole](https://pi-hole.net) is a network level ad blocker, which sends all requests towards ad networks to the ~ v o i d ~. 

Installation is simple by using Docker, a `docker-compose.yml` example is provided with the image \(and it should be an international war crime not to provide a docker-compose.yml alongside your image\): [https://github.com/pi-hole/docker-pi-hole](https://github.com/pi-hole/docker-pi-hole).

You'll probably want to put something like nginx in front of the web server so you can control who can access to the web interface \(as by default some things are public.\) If you change the default web interface port, you will also need to set an env variable that points to the new URL as by default the app expects to be running on port 80:

{% code title="docker-compose.yml" %}
```yaml
services:
    pihole:
        # ...
        ports:
            - '8001:80/tcp'
        environment:
            VIRTUAL_HOST: 'your.pihole.host:8001'
```
{% endcode %}

## strongSwan \(VPN\)

strongSwan is an IKEv2 VPN server, which is cool because IKEv2 is built in to most major mobile and desktop OSs, meaning you don't have to install any additional software. 

TODO: Write how to set this up \(and actually set it up, once I yell at my ISP\)



