---
title: PAIcoin Full Node With Tor Support
date: 2019-03-18 15:58:00 -07:00
tags:
- PAIcoin
---

Requirements:
Ubuntu 16.04 LTS

This HOWTO assumes you will be setting up a seed node with no wallet enabled. It is noted in steps the option to add wallet support if desired.

## Install dependencies:

`sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils python3`

### Boost dependencies:

`sudo apt-get install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev`

**If you wish to install wallet with QT install:**

For RPC wallet:

`sudo apt-get install software-properties-common`  
`sudo add-apt-repository ppa:bitcoin/bitcoin`  
`sudo apt-get update`  
`sudo apt-get install libdb4.8-dev libdb4.8++-dev`  

For QT support:

`sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler`

## Install Tor PPA and packages:

`sudo vim /etc/apt/sources.list`  

**Add these two lines:**

`deb http://deb.torproject.org/torproject.org xenial main`   
`deb-src http://deb.torproject.org/torproject.org xenial main`  

**Now add the PPA key and install Tor:**

`gpg --keyserver keys.gnupg.net --recv 886DDD89`   
`gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | sudo apt-key add -`  
`sudo apt-get update`  
`sudo apt-get install tor deb.torproject.org-keyring`  

## Grab paicoin source:

`cd ~/`  
`git clone https://github.com/projectpai/paicoin.git`  
`cd paicoin`  
`./autogen.sh`  
`./configure --disable-wallet`  
`make`  
`sudo make install`  

_If building with RPC wallet and QT remove_ `--disable-wallet`  

## Edit the Tor config:

`sudo vim /usr/share/tor/tor-service-defaults-torrc`  

**Make sure the following is anywhere in config:**

`ControlPort 9051`  
`CookieAuthentication 1`  
`CookieAuthFileGroupReadable 1`  

**Restart Tor to ensure changes take effect:**

`sudo service tor restart`  

**Add your paicoind user to group Tor group:**

`sudo usermod -a -G debian-tor <username>`  

**Verify user is now in group:**

`id <username>`  

Close SSH session and connect again or if local machine, close terminal session and reopen a new one.

**Test paicoind is working with Tor support:**

`paicoind -daemon -listenonion -debug=tor`  

**Check debug log to ensure working:**

`tail -f ~/.paicoin/debug.log | grep tor`  

If all working should see something like:

`2018-08-01 21:15:07 tor: Got service ID fowrbdfxd53hchsp, advertising service fowrbdfxd53hchsp.onion:8567`  

_You now have a working PAI Coin full node with Tor support!_