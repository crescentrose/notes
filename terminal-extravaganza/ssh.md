# ssh

## Use Mosh for uninterrupted SSH access

`mosh` is a layer on top of SSH that adds fault tolerance and improves performance, which is especially useful on mobile networks and when using ssh from your phone or tablet.

Installing it is relatively easy:

```bash
# add the PPA
sudo add-apt-repository ppa:keithw/mosh
# update the package index
sudo apt update
# install mosh
sudo apt install mosh -y
# let it through the firewall
sudo ufw allow mosh
sudo ufw reload
```

You should then be able to connect via mosh from the device of your choice.

## Force password authentication

There might be an occasional situation in which you might want to authenticate using a password instead of connecting using your public key. Note that this will fail if the server has password authentication disabled.

```bash
# password only
ssh -o PreferredAuthentications=password user@127.0.0.1
# fallback to pubkey if server has password auth disabled
ssh -o PreferredAuthentications=password,publickey user@127.0.0.1
```

