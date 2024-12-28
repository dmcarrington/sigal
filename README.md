Sigal Image Gallery with LNBits Paywall
=========================================

Forked from Sigal - https://github.com/saimn/sigal

This is a fork of Sigal, a simple but powerful image gallery generator. It is based on the original work of [Saimon <saimn@saimn.org>](https://github.com/saimn/sigal). We use LNbits paywalls https://github.com/lnbits/paywall to allow users to pay for access to high-resolution images from the gallery.

Prerequisites
------------
* A suitable VPS with a static IP address, a domain name, and a DNS entry pointing to the static IP address.
* Python 3.8 or higher
* An available LNBits instance with a paywall enabled.

Installation
------------

In general, we follow the installation instructions from the original Sigal repository - https://sigal.saimon.org/en/latest/contribute.html.

### 1. Clone the repository:
```
git clone https://github.com/dmcarrington/sigal.git
cd sigal
virtualenv venv
. venv/bin/activate
```
Install sigal and its dependencies:
```
pip install -e
```

### 2. Create a configuration file:
Initialize the configuration file:
```
sigal init
```
Customise the configuration file to add the new fields for the LNBits paywall.
```
keep_orig = True
paywall_amount = <what you want to charge, in sats>
paywall_remembers = "True"
paywall_url = <address of your LNBits paywall API, e.g. mydomain.com/paywall/api/v1/paywalls>
paywall_link_base = <address that paywalls will point to, e.g. mydomain.com/paywall/>
paywall_api_key = <your paywall API key>
deployment_url = <mydomain.com>
```

### 3. Build the gallery:
Copy the gallery to the server. This must all be a in a single root folder, but can contain subfolders, each or which will appear as a separate gallery.
```sigal build </path/to/gallery>```
This will generate the gallery in the build folder, and create the paywall links. Check in your paywall console within lnbits to verify that these exists. Check the properties of one of the paywall items to check that the link to the paywalled item is correct and is accessible.

### 4. Start the gallery:
```sigal serve -p80```
You may first need to run ```sudo setcap 'cap_net_bind_service=+ep' /usr/bin/python3.8``` to allow the server to bind to port 80.

Check that the gallery is accessible at the domain name you have configured, and check that the paywall links work.

### 5. Setup a service to run the gallery:
Stop the server with ```Ctrl-C```.
Create a service file:
```
sudo nano /etc/systemd/system/sigal.service
```

Paste the following into the file:
```
[Unit]
Description=sigal

[Service]
WorkingDirectory=/home/ubuntu/sigal
ExecStart=/home/ubuntu/sigal/venv/bin/sigal serve -p 80
User=ubuntu
Restart=always
TimeoutSec=120
RestartSec=30
Environment=PYTHONUNBUFFERED=1

[Install]
WantedBy=multi-user.target
```

Then reload the service daemon and start the service:
```
sudo systemctl daemon-reload
sudo systemctl start sigal
```

and check that it is running:
```
sudo systemctl status sigal
```