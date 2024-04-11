---
title: Scraping Dark Web
date: 2024-04-10 15:19
categories:
  - CODING
tags:
  - python
  - dark-web
---


### Installing package
---


Let's try and automate interacting with sites on the Dark Web.
Start by installing the tor-package by running following command:
```shell
sudo apt install -y tor
```

This will allow us to tunnel through the onion router and move through different relays and nodes.
> There are 3 types of relays in the tor network:
>>1. **Entry Relays**: Entry relays are the first point of contact for Tor users.
>>2. **Middle Relays**: Middle relays receive encrypted traffic from entry relays and forward it to other relays or exit relays.
>>3. **Exit Relays**: Exit relays are the last hop in the Tor network before the traffic exits onto the regular internet.
{:.prompt-info}

An important command that is bundled with the tor-package is `torify`.
It's a wrapper for torsocks and tor. You can put it in front of other commands you want to run and it will tunnel them through tor.

![Manpage Tor](/assets/img/dws/sdw1.png)

### Configuration
---


If we try to use `curl` and wrap it we see that we still need to configure it.

![Request fail](/assets/img/dws/sdw2.png)

We open the Tor configuration file:

```shell
sudo vim /etc/tor/torrc
```

Make sure the 'ControlPort' is enabled. Enabling the control port allows external applications/scripts to interact with the tor process.

![/etc/tor/torrc configuration](/assets/img/dws/sdw3.png)

CookieAuthentication should be set to 1. Tor will generate a cookie file containing a random value that serves as an auth-token ('**control_auth_cookie**'). The control port will only accept connections from processes that provide this token.

Restart the tor-service:
```shell
sudo service tor restart
```


### Curl request
---

If we want to make the curl request, we have to specify the SOCKS5 proxy to use.
We do this by using the `--socks5-hostname` flag. Specify our current localhost and the port we want to use. 

![socks5-hostname flag man](/assets/img/dws/sdw4.png)

```shell
curl --socks5-hostname 127.0.0.1:9050 http://lockbit7z2jwcskxpbokpemdxmltipntwlkmidcll2qirbu7ykg46eyd.onion/
```


> Make sure tor service is running. Check by running `netstat -tuln` and look for ports 9050/9051.
{:.prompt-warning}

![Listing ports](/assets/img/dws/sdw5.png)

### Simple Python Script
---

Install the required python lib by running following cmd:
```shell
pip install requests_tor
```

Simple example:
```python
from requests_tor import RequestsTor

requests = RequestsTor(tor_ports=(9050,), tor_cport=9051)
url = "http://lockbit7z2jwcskxpbokpemdxmltipntwlkmidcll2qirbu7ykg46eyd.onion/"
r = requests.get(url)

print(r.text)
```

We now have a way to automate making requests.
