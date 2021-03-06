IPOP Stats
==========

**Warning:** *This is under heavy development, and is not ready for use.*

*Gathers anonymous usage statistics of IPOP users.*

We need these statistics to justify our funding in grant proposals. They're
opt-in only, and the only information we collect is:

-   Your IP address
-   A randomly generated UUID
-   Client version number
-   The number of connections your node makes
-   If STUN or TURN is being used
-   The name of the controller (groupvpn or socialvpn)
-   Statistics about average connection lifetime

We'll never publish full database dumps, but we will publish general statistical
information from our database.

API Documentation
-----------------

The API still needs to be finalized and documented. For now, read the source to
see how the API works.

We follow the [JSend specification] for successes and failures.

[JSend specification]: http://labs.omniti.com/labs/jsend



Coding Style
------------

We follow [PEP 8] with a few exceptions:

-   Lines are 80 characters long (not 79). This includes docstrings and
    comments.
-   Explicit line continuation is often preferred over implicit, when it saves
    us from deeply nesting parenthesis

[PEP 8]: http://www.python.org/dev/peps/pep-0008/

Abuse
-----

It's possible for someone to write a client that abuses the API, spamming it
with artificially inflated statistics. We record IPs so we could perform some
analysis later to filter out these results, but heavily NATed environments could
complicate this.

Generally, we assume nobody will spam us with API calls, as there's no
motivation for an attacker to do so.

Upon setting up a production system, we should use some sort of rate-limiting,
to avoid being DoSed. The configuration file allows limiting based on UUID,
IPv4, and IPv6 addresses. This can be paired with [nginx's rate limiting
module][nginx limit req].

*Please don't be evil!*

Running server (without installing as an debian package)
--------------
```
sudo apt-get update
sudo apt-get install git python-pip python-flask python-sqlalchemy sqlite3 python-yaml
git clone https://github.com/ipop-project/ipop-stats.git
cd ipop-stats/ipopstat-0.12/DEBIAN
sudo bash preinst
sudo passwd ipop
Enter new UNIX password: ipop
Retype new UNIX password: ipop
passwd: password updated successfully
su ipop
Password:ipop
cd ../usr/share/ipop-stat
./run.py
```

now you can access this server through webbrowser. 

http://ip_address:8080/api

controller reports status info 

http://ip_address:8080/api/submit

sqlite database file is located /var/lib/ipop-stat/ipop-stat.db

Simple database query snippets below. 
```
$sqlite3 /var/lib/ipop-stat/ipop-stat.db
sqlite>select * from user;
sqlite>select * from ping;
```

Building Debian Packages
------------------------


Locate at the parent directory of ipop-stat then run below.

```
dpkg-deb --build ipopstat-0.14
```



Install and Running ipopstat from Debian Package
------------------------------------------------

```
sudo dpkg -i ipopstat-0.14.deb
sudo apt-get update
sudo apt-get -f install 
```



Note for next time
------------------------------------------------
I need to stop the ipop-stat before update. 
Add the command in preinst or something like "service ipop-stat stop"

Issues
------------------------------------------------
For some reason, "service ipop-stat start/stop" does not work. 




[nginx limit req]: http://nginx.org/en/docs/http/ngx_http_limit_req_module.html
