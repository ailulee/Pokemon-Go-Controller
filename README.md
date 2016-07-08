# Pokemon-Go-Controller

## iOS device as game controller
![Alt text](controller.png?raw=true "controller")  
Clone this xcode project and run on your iphone / ipad, this app actually perform a web server that tells your chosen location, your location will be wherever the poke ball is, so you may drag the map of press the buttons.

```
{"lng":"114.132530212402","lat":"22.3636264801025"}
```
This is what the app response via port 80 by http, so be sure to connect the iphone / ipad to your wifi network in order to gain access.

## Get controller message
```python
import xml.etree.cElementTree as ET
import urllib2
import json

lastLat = ""
lastLng = ""

def getPokemonLocation():
	try:
		response = urllib2.urlopen("http://your controller's ip/", timeout = 1)
		return json.load(response)
	except urllib2.URLError as e:
		print e.reason

def generateXML():
	global lastLat, lastLng
	geo = getPokemonLocation()
	if geo != None:
		if geo["lat"] != lastLat or geo["lng"] != lastLng:
			lastLat = geo["lat"]
			lastLng = geo["lng"]
			gpx = ET.Element("gpx", version="1.1", creator="Xcode")
			wpt = ET.SubElement(gpx, "wpt", lat=geo["lat"], lon=geo["lng"])
			ET.SubElement(wpt, "name").text = "PokemonLocation"
			ET.ElementTree(gpx).write("somewhere.gpx")
			print "Location Updated!", "latitude:", geo["lat"], "longitude:" ,geo["lng"]

def start():
	while True:
		generateXML()

start()
```
Edit readAndChangeXML.py ,change the urlopen address to your controller's ip and write to somewhere your gpx file you want to put. Be sure you remember where the gpx you put :)

![Alt text](receiver.png?raw=true "controller")  
If running normal, at console you should see something like this when your drag the map or press the buttons on your game controller.

![Alt text](blankProject.png?raw=true "controller")  
Create a blank single page app with your Xcode. Remember where you put the gpx file? Import the gpx file to your project without copying it, just referencing.

![Alt text](xcodeSimulate.png?raw=true "controller")  
Run this project on your iOS device that will actually run the Pokemon Go game, when running, at Xcode you will see a button to simulate location, so you see the option of your gpx file. Our next step is to constantly press this two buttons to simulate your location constantly and automatically.

http://stackoverflow.com/questions/4230867/how-do-i-simulate-a-mouse-click-through-the-mac-terminal/26687223  
By this, we can simulate a / some / lot of click(s) programmatically  
```
gcc -o autoClicker autoClicker.m -framework ApplicationServices -framework Foundation
```
compile the autoClicker.m with gcc at terminal.



