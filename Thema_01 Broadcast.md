- protects the recipient
- is a measure settled inside the communication network - nothing a user needs to change in his own
- guarantees perfect information theoretical unobservability of the receiver
	- no matter how many stations the attacker controls he does not know anything about the uncontrolled stations reception
	- surveilance does not change anything on that
- guarantees perfect information theoretical unlinkability
	- no relating traffic events visible
- broadcast must not happen trough a broadcast medium like satellite or WiFi
	- can also be something like a blackboard
	- someone writes, whoever can read
## Modifying Attacks
- observing attacks are no problem as described above
- modifying attacks are
- denial of service just breaks communication for all
	- scrambler
	- messing with radio network/equipment
- deanonymization
	- assume a scenario where a message (M) is sent by an attacker (A) and he wants to deanonymize the recipient (R)
	- assume further A is able to block connection of one to many participants
	- A could just block the connection to a set of the participants and thus decrease the size of the anonymity set
	- if multiple answers are required he could do that further, always halving the anonymity set until he find the replying station
	- other option is always block all but one station and round robin trough the whole set of participants
	- countermeasures
		- analogue - use a medium that does not allow blocking
			- hard to achieve
		- digital - add counters to message to allow stations that something is fishy
			- spots but never prevents inconsistencies
## Addressing
### Implicit/Explicit Addressing
- concerned with how a sender understands that a message is meant for him
- implicit addressing - a message contains an attribute upon each node must decide whether the message is for it or not
- explicit addressing - a message contains explicit information about who and maybe where a recipient is
### Invisible/Visible Addressing
- concerned with who can see the addressing attribute
- invisible addressing - only the right participant can correctly interpret an address meant for him
- visible addressing - everyone can interpret the address and compare it with others
	- messages with visible addresses are always linkable to the same recipient
	- his address is in this case equal to his identity
### Public/Private Addressing
- different aspect that is concerned with how to obtain an address
- public addresses - publicly available address indexes like yellow pages
- private addresses - assigned to single communication partners
	- can be done inside system e.g. "from" field in message 
	- or outside system
- neither says anything about people behind addresses
	- public addresses can be anonymous to everyone except the recipient that owns it
		- before usage
	- private addresses are assigned to one single person
- for private and public addresses, reuse means linkability of a message to a receiver
	- in public systems beginning with the first message
	- in private systems only if address is being reused
![[Pasted image 20240812144457.png]]
- visible public - attacker can just read visible address and lookup recipient by use of public address
- visible private - makes sense to change after use to avoid linkability between messages and recipient
	- keeping address the same kills private nature
- invisible public - i.e. published public key, brings the cost described below
- invisible private - e.g. prior secure exchange of symmetric key and see below
	- or use of sym key after establishing communication via public invisible
### Implementation of invisible implicit addressing
- uses redundancy in message content 
	- e.g. pre agreed tag or something that changes like a PRNG
- and asymmetric encryption system
- each message is encrypted with encryption key of addressed participant
	- completely -> end2end encryption 
	- or partially
- each station decrypts each message with its own key
	- verifies whether message is for him by checking the included redundancy
		- redundancy could be smth like a tag known by both sides
	- very costly to decrypt each and every message
- improvement: after initial message exchange key for synchronous encryption
	- faster and cheaper to implement and use
	- add a identifying bit that marks whether message is asym or sym encrypted
		- saves other station the hassle of decryption the sym encryption as long as no comm is ongoing with them
- yet another problem: multiple addresses per person
	- if multiple private keys per person all messages must be decrypted with all private keys
	- further, if more ongoing conversations all messages must be decrypted with all sym keys
- unlinkability is only as secure as the weakest encryption system used for addressing
	- weak asym encr system -> weak unlinkability
- instead of symmetric encryption symmetric authentication can be used
	- added redundancy is MAC of message
	- recipient can verify MAC by calculating it by himself

![[invisible_implicit_addressing]]
### Example: BitMessage
- uses invisible, implicit and private addressing and broadcast
	- address: hash(public encryption key, public signature test key)
- works with a P2P overlay system where single nodes store and forward messages
	- other nodes can pull from other nodes
- messages 
	- are digitally signed
	- elliptic curve encryption
	- proof of work
		- against spam
		- and in that sense DoS
### Implementation of visible implicit addressing
- just add a recipient field to the message
- all stations check whether the message is for them
- using multiple addresses is possible
- even just adding a random number gathered from a pseudo random number generator works
	- prior exchange of seed allows other side to generate same number and understand message is for him


### Equivalence of invisible implicit addressing and encryption
- invisible public addressing <==> asymmetric encryption
	- <= direction given trough use of pub keys as addresses
- invisible private addressing <==> symmetric encryption
	- <= direction given trough use of sym key as addresses
- => i.e. you have no encryption but just some means of addressing the other side without other knowing
	- message intended for the receiver = 1
	- message not for the receiver = 0
	- allows you to send arbitrarily long messages and only recipients understands it
## Fault tolerance and modifying attacks
- generally stations should not accept faulty broadcast messages
	- problem: sending info that a message was bad reveals recipient status
- when receiving we only save so many messages
	- that may cause problems if we want to restore or resync at some point
	- also with ongoing numbering or smth 
	- only in script - I don't get it

