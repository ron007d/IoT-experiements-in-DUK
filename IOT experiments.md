<div align = 'center'>

# Assignment - IOT Experiments
### **Bishwajit Kumar Poddar**
#### **Msc MI** 
#### **Roll - NuLL**

---

</div>

1. Configure Raspberry Pi on the system and explore its GUI
2. Get Values for IR,  LDR, PIR, UltraSonic, Rain, and  Sound sensors using Raspberry.
3. Uploading values of IoT sensors in the Cloud
4. Implement IoT Core service to deploy sensor values on AWS Cloud.
5. Establish wireless communication between 2 Raspberry pi using NRF and Bluetooth modules.
6. Establish Wireless communication between 2 Arduinos using NRF and Bluetooth modules.
7. Establishing client server communication
8. Sniffing messages communicated between client and server
9.  Configure ESP8266 using Aurdino IDE and configure GPIOs to Run the Hellp World Program ( Using LED )
10. Configure ESP8266 using Aurdino IDE and display characters in the Seven Segment Display.
---

# Configure Raspberry Pi on the system and explore its GUI

- To setup raspberry pi we need to flash official / arm images to raspberry. To do so, we can use the raspberry pi imager which is a cloned tool of etcher. It will download the raspberry pi image and flash it to the microsd card which will be used to boot Raspberry pie.


![Raspberry pi image](https://assets.raspberrypi.com/static/md-bfd602be71b2c1099b91877aed3b41f0.png)

*Figure: Raspberry pi imager*

- Choose the OS that you want to run on raspberry pi. And choose the storage which is the microsd card which will be inserted. after selecting click on write and image will be written to the micro-sd card. Just plug the micro-sd to raspberry pi and give power and it will boot on will show raspberry OS booting screen.

- Explore the OS. Open terminal and use `apt update` command to update the libraries.

<br>
<br>

# Get Values for IR,  LDR, PIR, UltraSonic, Rain, and  Sound sensors using Raspberry.

1. # IR
    - This sensor emits IR beam and in return senses the light which is bouncing back to it.
    - If a object is close enough then this sensor can detect the object and set -off alarm. But for this experiment the python code only gives a bash output.

    ***Souce Code***
    ```python
    import RPi.GPIO as GPIO
    import time

    sensor = 16
    #buzzer = 18

    GPIO.setmode(GPIO.BCM)
    GPIO.setup(sensor,GPIO.IN)
    #GPIO.setup(buzzer,GPIO.OUT)

    #GPIO.output(buzzer,False)
    print("IR Sensor Ready.....")
    print(" ")

    
    while True:
    if not GPIO.input(sensor):
        #print(GPIO.input(sensor))
        #GPIO.output(buzzer,True)
        
        print("Object Detected")
        time.sleep(0.5)
    else:
        print("Object not Deteceted")
        time.sleep(0.5)

    ```
2. # LDR

    - Light sensor detects light and give output as binary if its bright or not.

    ***Source Code***
    ```python
    import RPi.GPIO as GPIO
    import time
    GPIO.setmode(GPIO.BCM)
    GPIO.setwarnings(False)
    LIGHT_PIN = 24
    GPIO.setup(LIGHT_PIN, GPIO.IN)
    lOld =  not GPIO.input(LIGHT_PIN)
    print('Starting up the LIGHT Module (click on STOP to exit)')
    time.sleep(0.2)
    while True:
        if GPIO.input(LIGHT_PIN) <= 10:
            if GPIO.input(LIGHT_PIN):
            print ('\u263e')
            else:
            print ('\u263c')
        lOld = GPIO.input(LIGHT_PIN)
        time.sleep(0.2)
    ```

3. # PIR
    - its senses movement in the environment using IR data.
    
    ***Source Code***
    ```python
    import RPi.GPIO as GPIO
    import time
    GPIO.setwarnings(False)
    GPIO.setmode(GPIO.BOARD)
    GPIO.setup(11, GPIO.IN)         #Read output from PIR motion sensor
    GPIO.setup(3, GPIO.OUT)         #LED output pin
    while True:
        i=GPIO.input(11)
        if i==0:                 #When output from motion sensor is LOW
            print("No intruders",i)
            GPIO.output(3, 0)  #Turn OFF LED
            time.sleep(0.1)
        elif i==1:               #When output from motion sensor is HIGH
            print("Intruder detected",i)
            GPIO.output(3, 1)  #Turn ON LED
            time.sleep(0.1)
4. # UltraSonic

    - For this experiment we were given ultrasonic sensor and resistors to calculate the distance from sensor to any object.
        - Ultrasonic send a sound signal.
        - It bounces from the object.
        - From the time the sound wave came back we calculate the    distance
    
    ***Source Code***
    ```python
    import RPi.GPIO as GPIO
    import time

    GPIO.setmode(GPIO.BCM)

    GPIO_TRIGGER = 18
    GPIO_ECHO = 24

    GPIO.setup(GPIO_TRIGGER , GPIO.OUT)
    GPIO.setup(GPIO_ECHO, GPIO.IN)

    def distance():
        GPIO.output(GPIO_TRIGGER, True )
        time.sleep(0.00001)
        GPIO.output(GPIO_TRIGGER, False )
        
        starttime = time.time()
        stoptime = time.time()
        
        while GPIO.input(GPIO_ECHO)==0:
            starttime = time.time()
        
        while GPIO.input(GPIO_ECHO)==1:
            stoptime = time.time()
        
        timeescaped = stoptime - starttime
        
        distance = (timeescaped * 34300) /2
        return distance

    if __name__ == '__main__':
        try:
            while True:
                dist = distance()
                print(f'Measured distance = {round(dist)}')
                # add the requets code here + imported
                time.sleep(0.2)
            
        except KeyboardInterrupt:
            print('why you stopped ???????????????????')
            GPIO.cleanup()
    ```


5. # Rain Sensor
    - This sensor uses a simple circuit with many iron plates with minimum distance. if a water drops fall on the plate then its connects the circuit and thus gives a positive result. As water with minerals are good conductors of electricity. Water used in this filtered water (regular drinking )

    ***Source Code***
    ```python
    from time import sleep
    from gpiozero import Buzzer, InputDevice
    
    #buzz	= Buzzer(13)
    no_rain = InputDevice(18)
    
    def buzz_now(iterations):
        for x in range(iterations):
            #buzz.on()
            sleep(0.1)
            buzz.off()
            sleep(0.1)
    
    while True:
        if not no_rain.is_active:
            print("It's raining - get the washing in!")
            #buzz_now(5)
            # insert your other code or functions here
            # e.g. tweet, SMS, email, take a photo etc.
        sleep(1)
    ```
    *i.e. The buzz codes are commented out as we were not given any buzzer. But pin 13 can be used for that as per the code*
6. # Sound Sensor
    - Detects the noise in the environment and eleminates the background noise from there it gives any noise greater than the ambient noise.

    ***Source Code***
    ```python
    #!/usr/bin/python
    import RPi.GPIO as GPIO
    import time

    #GPIO SETUP
    channel = 17
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(channel, GPIO.IN)

    def callback(channel):
        if GPIO.input(channel):
                print "Sound Detected!"
        else:
                print "Sound Detected!"

    GPIO.add_event_detect(channel, GPIO.BOTH, bouncetime=300)  # let us know when the pin goes HIGH or LOW
    GPIO.add_event_callback(channel, callback)  # assign function to GPIO PIN, Run function on change

    # infinite loop
    while True:
            time.sleep(1)
    ```

# Uploading values of IoT sensors in the Cloud

- To upload the values we will the basic http model of Request Reponse model
![Request Response model](https://www.researchgate.net/publication/311571526/figure/fig3/AS:438170157359106@1481479314691/HTTP-request-response-model.png)
*Figure basic Request Response Model*

- To achive this we will use a simple python library which comes with preinstalled http request reponse model as a web framework.
- Create simple web url in flask app which can access the post requests made by the IOT devices and give positive reponse.

***Python flask webframework code***
```python
from flask import Flask,request,jsonify,render_template,redirect

app = Flask(__name__)
gloabl_variable = 0

@app.route("/")
def hello_world():
    return redirect('/multidata')
    # return render_template('auto_update.html')

@app.route('/get_multiple_data')
def get_multidata():
    return jsonify(datas[::-1])

@app.route('/multidata')
def multidata():
    return render_template('multiple_ready.html')

@app.route('/set_data',methods=['GET','POST'])
def set_data():
    print(request.args.to_dict())
    global gloabl_variable
    gloabl_variable = request.args.get('distance')
    return 'thank you'

if __name__=='__main__':
    app.run(host='0.0.0.0',debug= True)
```
> Here the template that is renders

***basic html page syntax***

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Auto update page</title>
</head>
<body>
    <center>
        <h1>Distance</h1>
        <h2 id = 'rondechaka'>0</h2>
    </center>
    <script>

        var element = document.getElementById("rondechaka"); 
        setInterval(function() { 
            fetch('get_data')
                .then(res => res.json())
                .then(data => element.innerHTML=data.distance)
        }, 500);
    </script>
</body>
</html>
```
- Host this in the AWS EC2 server
- use the amazon ec2 server ip address to post the data from IOT using requests module.

***Example request module code***
```python
import requests
import random
import time
for i in range(100):
    x = requests.get(f'http://10.10.27.169:5000/set_data?distance={random.randint(0,99)}')
    print(x)
    time.sleep(1)
```


# Implement IoT Core service to deploy sensor values on AWS Cloud

- For this experiment we'll be using MQTT. MQTT is a lightweight, publish-subscribe, machine to machine network protocol for Message queue/Message queuing service. It is designed for connections with remote locations that have devices with resource constraints or limited network bandwidth.

- AWS cloud give IoT core option and MQTT service with free SSL encryption support.

- First go to `aws.amazon.com`
![amazon website](./screenshots/Screenshot%202022-12-17%20at%2018-22-47%20AWS%20Management%20Console.png)
*Figure: AWS home webpage*
- From here go to `IoT Core`
![amazon Iot core page](./screenshots/Screenshot%202022-12-17%20at%2018-23-17%20AWS%20IoT.png)
*Figure: IOT core webpage*
- From here go to `All device > Things`

    ![all device things](./screenshots/Screenshot%202022-12-17%20at%2018-23-54%20AWS%20IoT.png)
    
    *Figure: ALl device to things navigator*
- click on things and this page will appear
![things page](./screenshots/Screenshot%202022-12-17%20at%2018-24-15%20AWS%20IoT%20-%20Manage%20-%20Things.png)
*Figure: Things page in AWS IoT Core*
- Click on create thing and after that this page will appear
![aws create thing page](./screenshots/Screenshot%202022-12-17%20at%2018-24-26%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things.png)
*Figure: AWS create thing page with many and single things creation*

- Click on next and specify the name
![specify the name](./screenshots/Screenshot%202022-12-17%20at%2018-24-47%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
    > For my Case I am using `ron-raspberry`
![name for thing](./screenshots/Screenshot%202022-12-17%20at%2018-25-50%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)

- Now in the thing type 
    - create a thing type
    ![thing type](./screenshots/Screenshot%202022-12-17%20at%2018-26-00%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
    - specify the name as `pi`
    > Change the thing type if needed in this case we are working with raspberry pi model 4 B
    
    ![pi name in things name](./screenshots/Screenshot%202022-12-17%20at%2018-26-28%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)

- Go to next and you will get a certification generation gateway
![certificate generation](./screenshots/Screenshot%202022-12-17%20at%2018-27-47%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
- Click on auto generate and go to next
![policy asking](./screenshots/Screenshot%202022-12-17%20at%2018-28-26%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
- Go to create policy where a new tab will open where you can create the policy and attactch with raspberry pi
![new page](./screenshots/Screenshot%202022-12-17%20at%2018-28-56%20AWS%20IoT%20-%20Security%20-%20Policies%20-%20Create%20policy.png)
- Give a name for my case I am using `ron-raspberry-policy`
![policy creation 1](./screenshots/Screenshot%202022-12-17%20at%2018-29-24%20AWS%20IoT%20-%20Security%20-%20Policies%20-%20Create%20policy.png)
- after that set the polcy action to `*` and policy resource to `*`
![policy action and resource](./screenshots/Screenshot%202022-12-17%20at%2018-32-02%20AWS%20IoT%20-%20Security%20-%20Policies%20-%20Create%20policy.png)
- Now you will see this screen where it confirms policy is created
![policy created screen](./screenshots/Screenshot%202022-12-17%20at%2018-32-31%20AWS%20IoT%20-%20Security%20-%20Policies.png)
- now close the TAB and go to previous tab where you will find this page
![policy selection](./screenshots/Screenshot%202022-12-17%20at%2018-33-09%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
- select `ron-raspberry-policy` and then `create thing` and you will get this screen
![created thing page ](./screenshots/Screenshot%202022-12-17%20at%2018-36-39%20AWS%20IoT%20-%20Manage%20-%20Things%20-%20Create%20things%20-%20Create%20single%20thing.png)
- Now as shown download device `certificate + Public key + private key`
- Then click on `Done`
- Go to certificates and attach to thing you created.
    ![certificate page](./screenshots/Screenshot%202022-12-17%20at%2018-39-12%20AWS%20IoT%20-%20Security%20-%20Certificates.png)
    - Select action and then select `Attatch to a thing`
    ![action](./screenshots/Screenshot%202022-12-17%20at%2018-39-40%20AWS%20IoT%20-%20Security%20-%20Certificates.png)
    - Type your thing name select and click on attatch to thing
    ![attatch](./screenshots/Screenshot%202022-12-17%20at%2018-40-19%20AWS%20IoT%20-%20Security%20-%20Certificates.png)

- Clone the repo `https://github.com/binaryupdates/aws-raspberrypi`
- This repo has only this files
![screen shot of folder](./screenshots/Screenshot%20from%202022-12-17%2018-42-20.png)
- now COPY the three files here
![screen show of folder after copy](./screenshots/Screenshot%20from%202022-12-17%2018-43-33.png)
- Now change the piython.py to this code where change file of certificate and change the of private key
```python
import time
import paho.mqtt.client as mqtt
import ssl
import json
import _thread
import RPi.GPIO as GPIO

GPIO.setmode(GPIO.BCM)
GPIO.setup(21, GPIO.OUT)

def on_connect(client, userdata, flags, rc):
    print("Connected with result code "+str(rc))


client = mqtt.Client()
client.on_connect = on_connect
client.tls_set(ca_certs='./rootCA.pem', certfile='./28a0b8ae5536ddb6125f5529e2727caa3d9696ae08422b85024ca699588ad0f9-certificate.pem.crt', keyfile='./28a0b8ae5536ddb6125f5529e2727caa3d9696ae08422b85024ca699588ad0f9-private.pem.key', tls_version=ssl.PROTOCOL_SSLv23)
client.tls_insecure_set(True)
client.connect("a1cqo4hn4vc7c8-ats.iot.ap-southeast-1.amazonaws.com", 8883, 60) #Taken from REST API endpoint - Use your own. 


def intrusionDetector(Dummy):
    while(1):
        x = GPIO.input(21)
        if(x==0): 
            print("Just Awesome") 
            client.publish("device/data", payload="Hello from BinaryUpdates!!" , qos=0, retain=False)
        time.sleep(5)

_thread.start_new_thread(intrusionDetector,("Create intrusion Thread",))
    
client.loop_forever()
```

> Here the client.connect URL you can find in the aws its different from others.<br>Go to settings in aws and in the enpoint you will find the url of yours
    ![aws settings for endpoint](./screenshots/Screenshot%202022-12-17%20at%2018-46-46%20AWS%20IoT.png)
    <br>From here you will see this appear
    ![aws end point url](./screenshots/Screenshot%202022-12-17%20at%2018-47-02%20AWS%20IoT%20-%20Settings.png)
    <br>Copy the end point and paste in
    
    
```python
client.connect("a1cqo4hn4vc7c8-ats.iot.ap-southeast-1.amazonaws.com", 8883, 60) #Taken from REST API endpoint - Use your own. 
```
- you need to install paho-mqtt pypi
```terminal
pip install paho-mqtt
```
- run the code and you will see this kind of output
```console
(pt_venv) ron@ron-linux:~/SECONDARY_SSD/GITHUB/aws-raspberrypi$ python3 pipython.py 
Just Awesome
Connected with result code 0
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
Just Awesome
```
- TO if its working go to AWS and click on MQTT Test Client and type the topic name `device/data` and click on subscribe
![topic subscription](./screenshots/Screenshot%202022-12-17%20at%2019-04-03%20AWS%20IoT%20-%20MQTT%20test%20client.png)
- The output will be like this
![output](./screenshots/Screenshot%202022-12-17%20at%2019-04-14%20AWS%20IoT%20-%20MQTT%20test%20client.png)

> <center> IOT message is publishing

# Establish wireless communication between 2 Raspberry pi using NRF and Bluetooth modules.
> add data
# Establish Wireless communication between 2 Arduinos using NRF and Bluetooth modules.
---
### **Wireless communication between Audrino and Android mobile using blueooth**
---
- For this experiment we have used an arduino to interface a bluetooth device with it. after that connect the bluetooth to mobile and in bluetooth console mobile app, send string data. which will be shown in serial console in arduino.

    ***Source Code***
    ```cpp
    #include <SoftwareSerial.h>
    SoftwareSerial EEBlue(10, 11); // RX | TX
    void setup()
    {
    
    Serial.begin(9600);
    EEBlue.begin(9600);  //Default Baud for comm, it may be different for your Module.
    Serial.println("The bluetooth gates are open.\n Connect to HC-05 from any other bluetooth device with 1234 as pairing key!.");
    
    }
    
    void loop()
    {
    
    // Feed any data from bluetooth to Terminal.
    if (EEBlue.available())
        Serial.write(EEBlue.read());
    
    // Feed all data from termial to bluetooth
    if (Serial.available())
        EEBlue.write(Serial.read());
    }   
    ```
    > i.e. Used serial write as we can get one line if used serial print ln then each word will take single line in the console.

# Establishing client server communication
- By using request module in the client side we can establish the connection between server
- For this experiment we can use the client side `requests.py`
- and Server situated in another place.
```python
import requests
import random
import time
for i in range(100):
    x = requests.get(f'http://10.10.27.169:5000/set_data?distance={random.randint(0,99)}')
    print(x)
    time.sleep(1)
```

---
# Sniffing messages communicated between client and server
- For this experiemnt we have used wireshark.
    - install wireshark in ubuntu
        - Run this commands one by one
        ```bash
        sudo add-apt-repository ppa:wireshark-dev/stable
        sudo apt update
        sudo apt install wireshark
        ```
- run wireshark using `sudo` command
    ```bash
    sudo wireshark
    ```
![images wireshark](./screenshots/Screenshot%20from%202022-12-19%2013-38-58.png)
*Figure: wireshark starting screen*
![image wireshark](./screenshots/Screenshot%20from%202022-12-19%2013-39-40.png)
*Figure: Capturing package in wireless card*
- ## another way is to use `spacy`
- run this command to install python scapy
    ```bash
    sudo apt install python scapy
    ```
- copy and paste this code
    ```python
    #!/usr/bin/env python
    import scapy.all as scapy
    import argparse
    from scapy.layers import http
    def get_interface():
        parser = argparse.ArgumentParser()
        parser.add_argument("-i", "--interface", dest="interface", help="Specify interface on which to sniff packets")
        arguments = parser.parse_args()
        return arguments.interface

    def sniff(iface=None):
        scapy.sniff(store=False, prn=process_packet)

    def process_packet(packet):
        if packet.haslayer(http.HTTPRequest):
            print(f"[+] Http Request >>   {packet[http.HTTPRequest].Host} to destination {packet[scapy.IP].src}  port number {packet[scapy.TCP].sport} location : {packet[http.HTTPRequest].Path}")
            if packet.haslayer(scapy.Raw):
                load = bytes(packet[scapy.Raw].load)
                print(load)
                keys = ["username", "password", "pass", "email"]
                # for key in keys:
                #     if key in load:
                #         print("\n\n\n[+] Possible password/username >> " + load + "\n\n\n")
                #         break

    # iface = get_interface()
    sniff()
    ```

- run using `sudo`
    - For example
    ```bash
    sudo python3 sniffer.py
    ```

---

#  Configure ESP8266 using Aurdino IDE and configure GPIOs to Run the Hellp World Program ( Using LED )


- The ESP8266 board comes with built in LED. Which can be used to blink with a delay of 250ms.
- After connecting the ESP8266 to we need to import/download ESP8266 binaries so that machine can compile the code compatible with the binaries in the ESP8266
    > The steps the bellow / if already done not needed
    - COPY the link bellow
    ```link
    http://arduino.esp8266.com/stable/package_esp8266com_index.json
    ```
    - Choose Preferences in the File menu and enter the copied code in Additional Board Manager URLs part. Then press OK. 
        ![selection image](https://hackster.imgix.net/uploads/attachments/944045/nod_2_JO1yYH4jJF.png?auto=compress%2Cformat&w=680&h=510&fit=max)
    - Search the word ESP8266 in Boards>boards manager from Tools menu. Then install ESP8266 boards. After complete installation, you will see the INSTALLED label on ESP8266 boards.
        ![step 2 above](https://hackster.imgix.net/uploads/attachments/944046/nod_1_lVizrFmm5b.png?auto=compress%2Cformat&w=680&h=510&fit=max)
    - After these two steps, you can see ESP8266 based boards such as NodeMCU in your Arduino IDE boards list, and you can choose your desired board to upload the code.
        ![step 2 above](https://hackster.imgix.net/uploads/attachments/944048/screen1_9uHCz1y0zV.jpg?auto=compress%2Cformat&w=680&h=510&fit=max)
    - Copy and run this simple code in your audrino IDE

    ```cpp
    void setup() {
    // initialize digital pin LED_BUILTIN as an output.
    pinMode(LED_BUILTIN, OUTPUT);
    }

    // the loop function runs over and over again forever
    void loop() {
    digitalWrite(LED_BUILTIN, HIGH);  // turn the LED on (HIGH is the voltage level)
    delay(250);                      // wait for a second
    digitalWrite(LED_BUILTIN, LOW);   // turn the LED off by making the voltage LOW
    delay(250);                      // wait for a second
    }
    ```
---
# Configure ESP8266 using Aurdino IDE and display characters in the Seven Segment Display.
---
- Connect the seven segment display in this manner
![how to connect seven segment display](https://939506.smushcdn.com/2600043/wp-content/uploads/2/2/1/5/22159166/7-segment-display-with-nodemccu-over-wifi-using-arduino-ide_orig.png?lossy=0&strip=1&webp=1)
- then run this code

**Source Code**
```cpp
#include <ESP8266WiFi.h>
 
//const char* ssid = "Your SSID";
//const char* password = "Your Wifi Password";

//Seven segment pins attachecd with nodemcu pins  
int a = 0;  //Gpio-0 with a of 7 segment display   
int b = 1;  //Gpio-1 with b of 7 segment display    
int c = 2;  //Gpio-2 with c of 7 segment display  
int d = 3;  //Gpio-3 with d of 7 segment display  
int e = 4;  //Gpio-4 with e of 7 segment display   
int f = 5;  //Gpio-5 with f of 7 segment display  
int g = 16; //Gpio-16 with g of 7 segment display

void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(a, OUTPUT);
  pinMode(b, OUTPUT);
  pinMode(c, OUTPUT);
  pinMode(d, OUTPUT);
  pinMode(e, OUTPUT);
  pinMode(f, OUTPUT);
  pinMode(g, OUTPUT);
}

void loop() {
  digitalWrite(a, LOW);
  delay(250);
  digitalWrite(b, LOW);
  delay(250);
  digitalWrite(c, LOW);
  delay(250);
  digitalWrite(d, LOW);
  delay(250);
  digitalWrite(e, LOW);
  delay(250);
  digitalWrite(f, LOW);
  delay(250);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a,HIGH);
  digitalWrite(b,HIGH);
  digitalWrite(c,HIGH);
  digitalWrite(d,HIGH);
  digitalWrite(e,HIGH);
  digitalWrite(f,HIGH);
  digitalWrite(g,HIGH);
  delay(250);
  digitalWrite(a, HIGH);
  digitalWrite(b, HIGH);
  digitalWrite(c, HIGH);
  digitalWrite(d, HIGH);       //Displaying 1
  digitalWrite(e, LOW);
  digitalWrite(f, LOW);
  digitalWrite(g, HIGH);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, HIGH);      //Displaying 2
  digitalWrite(d, LOW);
  digitalWrite(e, LOW);
  digitalWrite(f, HIGH);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, HIGH);      //Displaying 2
  digitalWrite(d, LOW);
  digitalWrite(e, LOW);
  digitalWrite(f, HIGH);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, LOW);
  digitalWrite(d, LOW);      //Displaying 3
  digitalWrite(e, HIGH);
  digitalWrite(f, HIGH);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a, HIGH);
  digitalWrite(b, LOW);
  digitalWrite(c, LOW);
  digitalWrite(d, HIGH);
  digitalWrite(e, HIGH);     //Displaying 4
  digitalWrite(f, LOW);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, HIGH);
  digitalWrite(c, LOW);
  digitalWrite(d, LOW);
  digitalWrite(e, HIGH);     //Displaying 5
  digitalWrite(f, LOW);
  digitalWrite(g, LOW);
  delay(250);
   digitalWrite(a, LOW);
  digitalWrite(b, HIGH);
  digitalWrite(c, LOW);
  digitalWrite(d, LOW);    //Displaying 6
  digitalWrite(e, LOW);
  digitalWrite(f, LOW);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, LOW);
  digitalWrite(d, HIGH);
  digitalWrite(e, HIGH);   //Displaying 7
  digitalWrite(f, HIGH);
  digitalWrite(g, HIGH);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, LOW);
  digitalWrite(d, HIGH);
  digitalWrite(e, HIGH);   //Displaying 7
  digitalWrite(f, HIGH);
  digitalWrite(g, HIGH);
  delay(250);
  digitalWrite(a, LOW);
  digitalWrite(b, LOW);
  digitalWrite(c, LOW);
  digitalWrite(d, HIGH);
  digitalWrite(e, HIGH);    //Displaying 9
  digitalWrite(f, LOW);
  digitalWrite(g, LOW);
  delay(250);
  digitalWrite(a,HIGH);
  digitalWrite(b,HIGH);
  digitalWrite(c,HIGH);
  digitalWrite(d,HIGH);
  digitalWrite(e,HIGH);
  digitalWrite(f,HIGH);
  digitalWrite(g,HIGH);
  delay(250);
  
}
```
