![raspberry-logo](https://cloud.githubusercontent.com/assets/22894897/24567005/2d7de462-1632-11e7-978e-0afaa0e707ae.png)

# Blink + Pushbutton example

First step:

![blink](https://cloud.githubusercontent.com/assets/22894897/24591015/3ce471b2-17ce-11e7-8086-0284d7a9dcc4.gif)

Wiring:

<img width="1047" alt="screenshot 2017-04-04 21 32 03" src="https://cloud.githubusercontent.com/assets/22894897/24684682/2db7b152-197e-11e7-8574-b607c2d0940f.png">

Pin Reference:

<img src="https://cloud.githubusercontent.com/assets/22894897/24590712/70a0ade6-17c8-11e7-8bee-d1370a0c90c5.jpg" width="65%" height="65%"/>

Using VNC Viewer to control the Raspberry Pi from your phone:

<img src="https://cloud.githubusercontent.com/assets/22894897/24590902/09942084-17cc-11e7-8dc2-abdb8249af2c.jpeg" width="50%" height="50%"><img src="https://cloud.githubusercontent.com/assets/22894897/24590905/12a9699a-17cc-11e7-8b2c-2034ce222da1.jpeg" width="50%" height="50%">

Download VNC Viewer from here: https://www.realvnc.com/download/viewer/ and get creative!

Python Code:

```python
#Pushbutton + LED
#Author: Niam Moltta

import RPi.GPIO as GPIO
import time

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BOARD)
GPIO.setup(12, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(16, GPIO.OUT)

try:
    while True:
        inputValue= GPIO.input(12)
        if (inputValue == False): #Not pressed
            print("LED is blinking")
            GPIO.output(16, True) 
            time.sleep(1)
            GPIO.output(16, False)
            time.sleep(1)
        else: #Pressed
            GPIO.output(16, True) #LED ON
            time.sleep(0.03)
            print("LED is on")
except KeyboardInterrupt:
    print("Cleaning up GPIO...")
    GPIO.cleanup()
 ```

The instruction is that the LED must be blinking until you press the button.  When you press the button it has to be turned on until you release the button.

Demo:

![ledpush](https://cloud.githubusercontent.com/assets/22894897/24684928/4078caa4-1980-11e7-88bc-0ea03ba793ac.gif)

# Write a simple server
Print "Got a request" on Raspberry Pi screen when the server has received a request.

```python
#Author: Niam Moltta

import socket
import sys

ms = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
msinfo = socket.getaddrinfo(None, 1234)
print(msinfo[3][4])

try:
    ms.bind(msinfo[3][4])
except socket.error:
    print"Failed to bind"
    sys.exit()
ms.listen(5)

while True:
    conn, addr = ms.accept()
    data = conn.recv(1024)
    if not data:
        break
    print "Got a request!"

    print data

conn.close()
ms.close()
```
You access the socket through a terminal window and send messages to the server.

![rpi](https://cloud.githubusercontent.com/assets/22894897/25349520/877bdd2e-28f8-11e7-9afb-ed8e3ee77dc6.png)

# Tweeting from Raspberry Pi

You need:

-A twitter account

-Go to www.apps.twitter.com

-Create your app

-Create your python program and run it from your raspberry pi

```python
from twython import Twython, TwythonError

#be sure to keep these keys safe or else, everybody could manage your twitter account

C_KEY = "your own API KEY"
C_SECRET= "your API SECRET"
A_TOKEN = "your ACCESS TOKEN"
A_SECRET = "your ACCESS TOKEN SECRET"

pipitan = Twython(C_KEY, C_SECRET, A_TOKEN, A_SECRET)
message= 'This is my Raspberry Pi tweeting. #myFirstTweet'
try:
    pipitan.update_status(status= message)
except TwythonError as e:
    print e

print status +" ------tweeted"
```

Tweeting from Raspberry Pi:

![2017-04-24-200516_1280x720_scrot](https://cloud.githubusercontent.com/assets/22894897/25362493/872b42f6-292a-11e7-83b7-35ffa2cc3eb1.png)

#myFirstTweet:

<img width="594" alt="screenshot 2017-04-24 20 21 08" src="https://user-images.githubusercontent.com/22894897/31022736-1500c4be-a510-11e7-8d65-6bae73ad1af1.png">

# Counting tweets

Python code:

```python
#Author: Niam Moltta

from twython import TwythonStreamer

C_K = "your key"
C_S = "your secret key"
A_T = "your token"
A_S = "your secret token"

tweetcount = 0
def increment():
    global tweetcount
    tweetcount = tweetcount+1

class MyStreamer(TwythonStreamer):
    def on_success(self, data):
        text = 'text'
        for text in data:
            increment()
            print tweetcount
            break
        if tweetcount == 3:
            print"I have to pee a lot"
            
stream = MyStreamer(C_K, C_S, A_T, A_S)
stream.statuses.filter(track="I have to pee")
```

# Use PWM to make an LED fade

The Wiring:

<img width="846" alt="screenshot 2017-04-28 10 46 52" src="https://cloud.githubusercontent.com/assets/22894897/25531429/03b874ec-2c00-11e7-9c07-6d8391e297ee.png">

Python Code:

```python
#Author: Niam Moltta

import RPi.GPIO as GPIO
import time
GPIO.setwarnings(False)
GPIO.setmode(GPIO.BOARD)
GPIO.setup(12, GPIO.OUT)
pwm = GPIO.PWM(12, 50)
pwm.start(0)
print "Let's fade!"
try:
    while True:
        for i in range(100):
            pwm.ChangeDutyCycle(i)
            time.sleep(0.02)
        for i in range(100, 0, -1):
            pwm.ChangeDutyCycle(i)
            time.sleep(0.02)
except KeyboardInterrupt:
    GPIO.cleanup() #Ctrl+C to exit
    print "Good bye!"
```

Program working:

![2017-04-28-104910_1280x720_scrot](https://cloud.githubusercontent.com/assets/22894897/25531578/a8b5001e-2c00-11e7-8a7f-e0f6c08b3767.png) 

You can press Ctrl+C to exit:

<img width="596" alt="screenshot 2017-04-28 10 51 11" src="https://cloud.githubusercontent.com/assets/22894897/25531607/c03d5f56-2c00-11e7-98f8-98d2e7eab4ae.png">

Demo:

<img width="70%" height="70%" src="https://cloud.githubusercontent.com/assets/22894897/25531226/62744ea8-2bff-11e7-9dcb-4448d6543105.gif"/>
<br>
<br>
<p align="center"><a href="https://github.com/Neelanjan-Goswami?tab=repositories" target="_blank"><br><button><img src="http://i.imgur.com/ERyS5Xn.png" alt="l'astra lab icon" width="50px" background="transparent" opacity="0.5" padding="0;"/></button></a></p><br><br>
