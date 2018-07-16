
# LouisGiraud2000
LPWAN Proof of location

This paper describes a possible future product, that is intended to identify users physically with GPS coordinates, that relies on a mesh network or loraWAN gateways connected to the blockchain.
The hardware made up of:

 

 - Gateways declared on the blockchain with GPS coordinates. In the rest of the document, they are called gateways.
   
 - Certification devices with loraWAN module, GPS sensors and HMI.(however, hardware GPS and HMI can be replaced by Bluetooth
   connection + smartphone...). In the rest of the document, they are
   called device. The owner of a device is called an user.

**The principle is the following :**
    Gateways are connected to the blockchain, and they receive certification requests from devices. They process the request as described below and sends a certification that the user (or at least its certification device) were located at a given moment at a given GPS position.
Several gateways can process the same authentification request so to avoid fake certifications.
 The goal is to ensure that the person behind an account really exists, and are located somewhere physically: For instance, in a 10000m2 area area, there cannot be 500.000 persons in the same time.

**Weakness:**
There must be enough gateways in a given area so the mesh can work.

**Prerequisites**
   

 -  The member must have an ID, and a public/private key (I'm not sure if ethereum provides natively such a mechanism or if we must implement it)
 -  The member must own a device as described 
 -  The member must be in a geographical zone where gateways are present
 -  There must exist a database where inserts are ciphered until a "cycle end", for instance every x seconds, the database sends a public update, with all the inserts in clear, but the inserts cannot be read before the end of the cycle: this allows a mechanism to avoid the fake gateways to read what the real ones sent and copy the messages to pretend they also received the lorawan messages. I think this is feasible if every inserter sends first a ciphered insert, and at the end of the cycle the key to uncipher: he cannot change afterwards the data he has just sent, and nobody can read his insert until he sent the key to uncipher.

     -  Step1: The device sends an auth request containing its ID and GPS coordinates via loraWAN.
     -  Step2: The gateways that receive the authrequest (they must be physically close, 10km max) request the authorisation to authentificate on the network.
     -  Step3: The network selects randomly 2 gateways and answer
     -  Step4: The selected gateways gather the public key associated with the ID to the network, cipher a randomly generated message and sends it to the device.
     -  Step5: The device uncipher each message with the user's private key and sends it back to the corresponding gateways
     -  Step6: The gateway verify that the received message matches, and if so sends a ACK to the network, with ID and GPS coordinates.
     -  Step7: The certification associated to the ID is renewed for some time (say 1 week) Another option is that the user associated with the ID receive some "reputation points"

**Why certify ?** 
The certification bind a physical device (and so a physical person) to a virtual account, so the confidence is increased for applications like vote. It also allows to certify that the person is physically located in a geographical area, for law issues.

**How are the gateway owners remunerated ?** 
Every time an user certify, it costs some ethereums that goes from his account to the gateway's one.

**How are the devices bound to an account ?** 
The account has a public/private key couple, and the device must know the key. This forces also the people that would want to cheat to provide their private keys to other individuals for pretending he's located in another area than he really is.

**How is it different ?**
 The difference with existing systems is that not only IP communication is used, but loraWAN, that allows direct device to device wireless communication and makes the things less cheatable: as anybody can use proxies for IP, the loraWAN gateways are more physically bound: nobody can send a message to a gateway if he's not physically close.
So, if anybody wants to connect a fake gateway to the network, it can be detected, because if a device asks for certification in the area where the fake gateway is declared, the fake gateway will be the only one that could not  notify that it received the message, and so it will gather bad reputation.
The network can also request the gateways to send a lorawan message, so the other gateways receive it and can certify that the gateway is really physically located in the declared area.


One of the difficulties is to calculate the reputation rate: 
    For the sensors, it can be a composite between the reputation rate of the associated account, and the sum of standard deviation between it and the close sensors (but careful with micro-climates).
    For the gateways, it can take in consideration: total communications, owner's reputation rate, number of incoherences (for instance how many missed calls with the certification devices)


*Missed call:* when a certification device sends a certification request, the gateways that are declared geographically close but missed to inform the network that a certification request were sent had a "missed call".



**Getting further with a concrete application on top : environmental data**

Here, we describe sensors that can sell data on the blockchain. They are not certification device, but sensor's owner can use a certification device to get better reputation, that will work on his sensor's reputation.

Say an organisation needs environmental data (temperature, hygrometry, lux, athmospherial pressure).
They send a request with gps position, a radius, a maximum price they want to pay, and a minimum reputation factor. 
All the gateways in the corresponding area receive the call from the network and sends a loraWAN request.
The sensors receive the request, and if they match the requirements, they send the requested data with its authentification.
The gateways receives all the data and sends them back. The sensor's declared GPS coordinate can be verified by triangulating all the gateways that received it.
The organisation pays a certain price for the data, and the money (ether for instance) is distributed between the gateways and the sensor that provided it. Each gateway and each sensor is free to ask for any price for the data, and the organisation will be able to select the gateways he wants with best price/reputaion rate.
The gateway will be able to select the sensor with the best price/reputation, or select randomly.The data is then available to everyone as long as it is present in the distributed database.

The organisations can also make deals between them to share the cost of the data.

The sensors contains a secondary private key, that is associated with an account.
