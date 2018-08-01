# skill-server-imirror

It displays weather, news and time, and also displays amazon alexa textfielfds
 from cards accepting the payload from an endpoint.

Motion sensor is implemented.


## Basic Setup of Raspberry Pi from fresh install for iMirror and its server:


Required: 
* Internet access

```bash
sudo raspi-config
```
Turn these on in raspi-config: 

* Wait for network on boot
* Optional: VNC

To remove black borders on screen
in /boot/config.txt uncomment 
```bash
disable_overscan=1
```

Disable XSession from Blanking  
```bash
sudo apt-get install xscreensaver
```
Once installed open the Pi's preferences and change screensaver preferences to disable screenblanking.

## Installation requirements

Clone the repo:
```bash
cd /home/pi && git clone https://github.com/Floyd0122/skill-server-imirror.git
```
Prevent any Compiling errors:
```bash
sudo apt-get install build-essential libssl-dev libffi-dev python-dev libglib2.0-dev
```
Install required packages:
```bash
sudo apt-get install python python-imaging-tk
```


Install script dependencies:

due to some pip errors recently we're usng explicit install (20/07/18)

```bash
cd skill-server-imirror && sudo python -m pip install -r requirements.txt
```

## Get API keys:
Go to [IpStack](https://ipstack.com/signup/free) and sign up for a free account.
Go to [darksky.net](https://darksky.net/dev/) and sign up for a developer account. 

Edit **interface.py** and replace the contents of WEATHER_API_TOKEN with the secret key provided on [Darksky's account page](https://darksky.net/dev/account/).
Edit **interface.py** and replace the contents of LOCATION_API_TOKEN with the secret key provided on [IpStack's account page](https://ipstack.com/quickstart/).
```python
WEATHER_API_TOKEN = '[TOKEN]' # replace with secret key provided at https://darksky.net/dev/account/
```

## Set up autostart
Add these lines to ~/.config/lxsession/LXDE/autostart to run things at startup
```bash
@lxterminal -e sudo /home/pi/skill-server-imirror/run.py #$HOME doesnt work
```

## Generate SSL Keys
```bash
openssl req -x509 -newkey rsa:4096 -nodes -out cert.pem -keyout key.pem -days 365
```
Copy the results into the /home/pi/skill-server-imirror/certs/ folder.

Put the motion sensor in the correct PINs
```
PIR SENSOR
GPIO GND [Pin 6]
GPIO 5V [Pin 2]
GPIO 7 [Pin 26]
```

## Running
Just restart the pi and it will work
Alternatively you can run it with this command:
```bash
sudo /home/pi/skill-server-imirror/run.py
```

# Docs
## Server

Listening over HTTPS on Port: 5005

### POST endpoint '/alexa'
Updates Interface with alexa's last response.


Required payload is json with 'title' and 'text' fields. Example: {'title' : 'Buzzbox', 'text' : 'turn everything on'}'
Returns {'response' : 'Update Ok'} if payload was parsed correctly

### GET endpoint '/toggle?command='
Responsible of changing the interface modules. Command is required, list of them:

on, off, board-on, board-off, weather-on, weather-off, clock-on, clock-off, guide-on-guide-off


Returns json with {'response': 'Update OK'} if one of the commands were used.
Returns json with {'Error' : 'Invalid Command'} if wrong argument was used.

