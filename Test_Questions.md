## 1. Name five methods / techniques for achieving anonymity.
- Broadcasting
	- recipient anonymity
- Private Message Service
	- recipient anonymity
- RING Network
	- sender/receiver anonymity
	- Busses, Taxis, Drunken cyclists
- DC-Net
	- sender/receiver anonymity
- Mix-Net
	- sender/receiver anonymity
## 2. Name and describe anonymity / privacy related protection goals.
- Anonymity 
	- being able to use a resource without disclosing their own identity
		- not even to the communicants
	- being unidentifiable within the anonymity set
		- the larger and more uniform behaving the anonymity set the better
- Unobservability
	- ensures that a user can use a resource without other being able to see that the resource is being used
	- uninvolved parties can see neither sending nor receiving
- Unlinkability
	- an attacker is not sufficiently able to figure out which items of interest are related
		- IOIs: user, actions, messages
	- in terms of anonymity
		- unlinkability between a user and an IOI
- Hiding
	- confidentiality of the transfer of user data
	- no one except the communicants can discover the existence of confidential communication

## 3. Which protection goal can be achieved with which anonymity technique?
- Anonymity
	- Broadcast, PMS
		- recipient anonymity
	- RING, DC-Net, Mix-Net
		- sender/recipient anonymity
- Unlinkability
	- Mix-Net
	- DC-Net
	- 
- Unobservability
	- Broadcast ?
		- No one can tell whether I'm receiving data from the broadcast
		- but everyone can see that messages are being sent
	- Mix-Net
		- siehst 
	- DC-Net ?
	- Ring 
		- Buses are unobservable says the slide
- Hiding
	- Mix-Net - 
	- Broadcast

## 4. How secure is the achieved anonymity in the case of broadcast with respect to observing / modifying attackers?
- observing is no problem
- modifying is
	- attacker can decrease anonymity set and thus identify a recipient by binary search
	- demands some kind of retry or retransmit protocol

## 5. Will end-to-end encryption solve the problem of modifying attacks on broadcast?
- no
	- I can still decrease the anonymity set
	- check the slides for that again
- problem is with attackers that are participants
	- then you are part of the network an can read the responses you get yourself
	- meaning you just need to bait people in responding and the rest stays the same as before
- see https://dud-bbb2.inf.tu-dresden.de/playback/presentation/2.0/playback.html?meetingId=252b0f62567027ae0f4c73d1fa790e1f72204db9-1623999954958
	- around minute 19
## 6. Describe the attacker model of the ring net.
- attacker is part of the system
	- sender, recipient and forwarder of each message
- attacker must not encircle a user/station
	- otherwise he can identify the encircled station as sender

## 7. Why do we need “digital signal regeneration” ?
- in order to avoid radio fingerprinting
- each station regenerates the signal so it changes its fingerprint 
	- or rather it's always the same fingerprint
	- as each stations last and next station is always the same

## 8. Describe how PIR / query and superpose work.
- PIR - private information retrieval == pms - private message service
- multiple server/databases
	- all with updated copies of the same amount of cells
- client asks multiple (>1, < max)
	- generates random query vectors to s-1 servers
	- XORs them with the real set vector
	- sends the random query vectors to s-1 servers
	- sends the XORed versions to the sth server
- receives responses from all servers
	- XORs locally to get the actually requested cell

## 9. Describe the attacker model.
- at least one database must not be under the control of the attacker
	- otherwise all requests/responses are known and the attacker can do the XOR himself
	- if only one honest DB is included the attacker can't know what cell was requested
## 10. Which “security level” could be achieved?
- no sé que es un security level
	- from le script
![[Pasted image 20240815100733.png]]
- apparently PIR is information theoretic secure if n-1 servers are malicious

## 11. Do you need to consider something while sending query vectors / receive the results form the database servers?
- traffic needs to be encrypted
	- otherwise listener on line is capable to calculate requested cell or response

## 12. Describe how one can save bandwidth from user to database(s)
- pre shared seeds for prng with all databaseses
	- generate random vector based on prng
	- create last vector and send to one db
		- somehow the other DBs know that the one was requested
	- all servers respond their responses
- you do the local XOR
	- but only sent one request to one server

## 13. How can we decrease the bandwidth from the databases to the user?
- same as above + you have a second prng for one time pads
- all servers XOR a one time pad into their local results
- send further to next server
- last one (the one that was requested) replies to user
	- only one response to user

## 14. What is the drawback of the two optimizations mentioned above?
- seed(s) must be exchanged in a secure manner 
	- yet another attack vector
- more computational load on DBs
- sufficient to steal a seed and not all values
	- on user and on db site
	- seed is potentially valid for long term and thus requests are long term readable

## 15. Regarding the optimization user to database: Do we still need to encrypt the one remaining query vector?
- yes as the remaining servers could be malicious and spy on the line for the last vector

## 16. Describe how the DC net works.

## 17. Describe the DC net attacker model.
- attacker can listen on all lines
	- attacker can delay messages to single participants
- attacker can be part of the network
	- thus receive everything
	- and also send
## 18. Which “level of security” can be achieved with the DC net?
- information theoretic security

## 19. What is the global sum?
- xor of what all people send

## 20. How to deal with collisions (sending of multiple messages at the same time)?

## 21. Describe how the reservation scheme works.

## 22. Describe how the collision resolution scheme based on mean calculation works.

## 23. How can/will recipient anonymity be achieved within the DC net?
- via broadcast

## 24. Are there attacks on the recipient anonymity possible?
- yes, like in broadcast

## 25. How can we prevent a successful attack on the recipient anonymity?
- with encryption to a certain extent
- with key calculation based on the whole history of received global sums

## 26. How does a Mix network work? What are the basic functions of a Mix?
- re-encryption
- discard repeats
- re-ordering
- keeping time and size uniform for in and output

## 27. Why “discard repeats”?
- to avoid replay attacks
	- decryption would look the same?

## 28. Why indeterministic asymmetric cryptography?
- 

## 29. How to achieve recipient anonymity?
- by recipient offering encryption along the way options

## 30. Why asymmetric crypto in Mixes?



## Fragen/Antworten vom DSEWiki

### Which anonymity technologies are there?
Broadcast, Query and Superpose, RING-Network, DC-Network, MIXes 

### What does broadcasting provide?
Recipient anonymity 

### What is an implicit address?
If you want to use broadcasting on routing services every node must decide through an attribute named implicit address which messages are actually for it 

### How can implicit addresses be implemented?
The usual implementation of invisible implicit addressing employs redundancy within the message content and an asymmetric encryption system. Every message is encrypted completely or partially with the encryption key of the addressed participant. After the decryption with the corresponding key the user station of the participant addressed can determine (with the redundancy inside the message) whether the message was designated for him. 

### How can a broadcast be attacked?
Attacker can choose certain recipients, and send the messages waiting for response. Using the method, attacker can decrease the anonymity. 

### How can one defend against that attack?
Insisting on error free receipt a sending of the user station is necessary. 

### What are the limitations of broadcasting?
Broadcasting is very inefficient if not supported by transport media; e.g. it's easy to do broadcasts in radio networks, but difficult in the internet. 

### How does query and superpose work?
On Scripts, P201 5.4.2 

### What are the optimizations?
Sending a Pseudo-Random Bit Generation (PRBG) seed instead of the random vectors Using padding keys and a local master to do the superposing 

### What must be encrypted on the lines?
Everything 

### If a PRBG seed is sent instead of the random vectors, and only one vector (which is calculated) is sent to a server, does that vector still need to be encrypted?
Yes! If the server that receives the only full vector is the only honest one (as per the attacker model) and all other stations are attackers, anonymity is lost 

### How does the DC-net work? 
Superposing in local subnets and broadcasting intermediate results 

### What are the optimizations?
Establishing a server, having that server do the addition (instead of the clients) and broadcast the result to all stations 

### What is the attacker model?
Attacker can disrupt communication and attack availability, but he won't violate anonymity 

### How is the DC NET (or DC+ NET) protected against modifying attacks? 
E.g. the attacker disrupts one line and a station gets a wrong global sum, what happens then? 
Is anonymity violated? Is availability violated?

In DC+ NET, keys depend on global sums from previous rounds. If only one station receives a corrupted message in one round, its global sum will be corrupted too, meaning that in the next round, its keys will be corrupted, and as a result, it will broadcast a corrupt local output and the global sum will be garbage. 
Availability is violated, but anonymity is not! 

### How do MIXes work?

In a direct sender anonymity scheme, how does a message look if there is one MIX in between the sender and the recipient?

The message could look like cm(z2,A,cr(z1,M)), where cm is the public key of the MIX, cr is the public key of the recipient, z1 and z2 are random numbers, A is the address of the recipient and M is the real message.