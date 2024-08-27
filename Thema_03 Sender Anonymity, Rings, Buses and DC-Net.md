## Sender Anonymity
- so far recipient was protected from identification, now we protect the sender
- for reasons we start with dummy messages
### Dummy Messages
- this is a measure for sender anonymity
- dummy messages must be indistinguishable from real messages
- attacker can not tell whether a message was real or a dummy and thus, who received the message
- also there is no protection against the actual recipient
	- whoever gets the real message knows who the sender is
- next the dummy messages introduce a huge overhead on the transport medium
	- need some space for real messages


## Ring Network
- actual RING network as in the early days
	- we have a physical ring net with stations along the ring
- attacker model
	- single station should not be encircled by attackers
![[ring_net_base]]
- message gets send from station to station
	- every station can store and forward or just forward
	- bit signal looks the same on the line
		- no reference to sending station as each station refreshes signal
		- also no distinguishing features between forwarded or created message
	- in other words: no radio fingerprinting possible
- this makes it possible for a sender to ensure that his message was not altered
	- it will end up back at his station and he can verify that it is what he sent
	- but not when it was received
- in order to identify a sender it is not sufficient to be the next node
	- you could not tell whether one has sent the message or whether he just forwarded
	- you'd also need to be the node before
	- only then you can tell what went in and what came out
	- as soon as there's at least one station in between you can only be sure that one of the stations in between changed/sent a message but not which explicit station
- RING also provides recipient anonymity
	- technically this is a broadcast
	- all messages go to everyone and you can't tell for whom it is
- medium access control
	- the token
	- only one station can send at a time - the one with the token
	- it can decide whether it wants to send while having the token or whether it wants to forward the token to the next station
- how to avoid one station keeps the token?
	- kinda DoS by just blocking it
	- can't enforce an upper limit of message to send while holding the token
		- if each station sends max number of messages and then forwards
		- the attacker sitting 3 stations behind knows that the order of messages is the same and the first belongs to the first sender, the fourth to the second, the seventh to the third
		- attacker can always start counting as soon as he gets the token 
			- then he knows a defined start of the following message sequence
	- but we also don't deliver any alternative to an upper limit in messages
		- thus we just sacrifice the anonymity for the DoS avoidance...I guess...
- sending/receiving should be time insensitive
	- otherwise an attacker might calculate whether someone sent or not based on measured time
	- hard to do if we assume different computer types
		- fast computer may process message quicker than slow computer
		- also preparing and sending message may take longer
- otherwise: time delta per station should be equal no matter what it did
### Ring availability
- one broken station -> ring broken
- create an braided ring with inner and outer ring connection
![[ring_error_fallback]]
- in consequence we have to enable some kind or error searching mode in which no station sends sensitive data
	- then we can analyse error fix bug and return to situation in which all operation is normal and everyone can send sensitive data
- also example above shows that a failing functioning configuration can lead to an invalid configuration afterwards
### GNU Net
- also a network of connected clients (overlay network)
- requests to other members of network wander around multiple hosts
	- forwarding hosts can decide whether to rewrite sender address or keep it as is
		- this helps with shorter ways backwards
		- but lowers anonymity
	- once message is received by recipient answer makes its way back depending on last sender address
- request is h(h(h(B))) not just B (B == Block)
	- why?
		- data is sent back encrypted so stations in between can't read it
		- hash of content is used as keyword
			- thus only hash can not be used
			- need at least hash(hash(block))
		- but provider has also to provide hash(hash(block))
			- as prove that content is actual requested content
			- only who has the content can provide hash of hash as going backwards from h(h(h(b))) is hard
				- because of hashing properties
- so in short
	- requester needs to know block or word to search for
	- sends out h(h(h(b)))
		- no one on route can calculate that back
	- receiver can calculate that and can reply requested block
		- response contains h(h(b)) as proof of ownership
		- no one on route can calculate that as hashing backwards is hard
		- response is also encrypted with password h(b)
	- sender gets response and can encrypt with h(b) as he knows what he searched for
	- whole network in between is a ring 
		- with shortcuts
		- and with the same problem: 
			- if an attacker encircles someone he can find out what he send and what he just forwarded

### Crowds
- ad hoc formed rings
- station sends to random routing descision and someone queries web service
- response takes same way back
- unencrypted requests at all nodes

## Busses, Taxis and Motorcycles
### Busses
- drive on rings
![[Pasted image 20240813165608.png]]
- attacker model
	- global observing outsider
	- observing participants
		- except sender/receiver
		- that means sender/receiver are not protected from each other
	- modifying attackers only wrt. availability
- achieved protection goals
	- sender/recipient anonymity
	- unobservability regarding sending/receiving of messages

![[bus_is_matric]]
- now our bus analogy turns into a two-dim matrix
	- messages can be placed in cell from some sender to some receiver
	- implicit addressing: one seat per user
	- uses dummy messages
		- if someone has nothing to send he just places dummy messages on the "seats"
	- time complexity is linear with participants
	- storage is quadratic to number of participants because we need to fit the matrix
- we avoid privacy risk with medium access control discussed in usual rings
	- so the problem with a max amount of messages that allows an attacker to understand who sent what
	- whenever the matrix comes along we send and we are certain that only we send
	- as we set dummy messages everywhere we do what is expected and do not tell anything by doing so
- matrix allows recipient to clearly identify sender and vice versa
- as if we don't have anything else to do we now reduce the storage complexity
	- from $n^2$ to some arbitrary assumption of messages sent at a time $s$ multiplied with yet another arbitrary storage parameter $k$ to achieve a bus size of $b =  s^2 * k$ 
	- we do so by allowing exactly one seat per sender
		- that is randomly picked when the sender wants to send
		- it places a message with layered encryption
		- deepest layer the actual recipient
			- outer layers all stations on the way
	- that achieves sender anonymity against the recipient
		- he just gets a message, last encrypted from prior station
![[Pasted image 20240813171536.png]]
- this is vulnerable to replay attacks
	- decryption is deterministic
	- scenario someone recorded all inputs and outputs to a station
	- he replays an old message to the next station
	- if the message was for him he'll reply another random value
	- if it was not for him it will look like it looked before (because the decryption is deterministic)
- next problem is randomness the actual recipient has to produce
	- random may look different then an encrypted message (valid cyphertext)
	- assuming standard RSA encryption we'd have some certain value range where our encrypted values can be in
		- just rolling a dice is a problem because it can yield higher, lower or non mod matching numbers
	- thus, better decrypt random garbage so you're certain that you are in the right 
		- **decrypt** not **en**crypt - to be certain that you're in the right domain
		- modulus value of RSA is just one
		- just use the crypt setup you have to not worry about it

### Re-encryption to avoid encryption along the way
- create two messages that look different but are in fact the same
- have c=Enc(e,m) e == public key
- want c'=Enc(e,m) with c != c'
- use ElGamal for that
	- $e=g^x$
	- Enc(m) = ($g^y$, $m*e^y$)
	-  homomorphic property: 
		- Enc(m1) * Enc(m2) = Enc($m1 * m2$)
		- Enc(1) does not change anything on message or resulting cyphertext
	- re-encryption:
		- Enc(m)^z = $g^y * g^z$,  $m*e^y*e^z$ = $g^{y+z}$, $m*e^{y+z}$ = $g^{y'}$,  $e^{y'}$
		- z is randomly choosen
		- g was the generator and e the public key
		- works if you know the public key
		- allows to go from one ElGamal cypertext to another
	- now universal re-encryption:
		- idea: don't only send encryption of the message but also encryption of value 1
			- Enc(m) = \[Enc(m), Enc(1)] = \[ (g^y, e^y), (g^y\', e^y\')]
		- now apply two random numbers to arrive at different cyphertext
			- z, z\'
			- Enc(m)^z,z\' = \[ Enc(m) \* Enc(1)^z , Enc(1)^z¸' ] = \[ (g^y+y\'\*z, e^y+y\'\*z), (g^y\'+y\'\*z\', e^y\'+y\'\*z\')] = \[(g^y'', m\*e^y\'\'), (g^y\'\'\', e^y\'\'\')]
			- so we achieve the same shape again Enc(m), Enc(1)
				- Enc(1) * Enc(m) = Enc( 1 * m) = Enc(m)
			- this works without knowing the public key e by just exponentiating with the zs
				- for whatever reason the 1 helps us
		- main takeaway:
			- start with cypertext
			- unknowing pub key
			- still able to create a new cypertext that is an encryption of the same message
			- this gives us unlinkability
				- as there can be two messages for the same content
- and this is useful for the busses because we now don't need to run along the path necessarily
	- we can simply encrypt the message once with the key of the recipient
	- then we send it along whatever path
	- each station that figures the message is not for it just applies universal re-encryption
		- thus always generating a different shape of the same cypertext
	- only the final station (who the message is for) encrypts and reads it
	- and can then still universally re-encrypt it to let its output look alike
![[re_encryption-with-busses]]

- proxy re-encryption
	- you have c=Enc(e,m) and another pub key e\'
	- you want a c\'=Enc(e\', m)
	- without revealing m
	- usecase would be a notary
		- receives cypher and other key
		- in case of emergencies, law-enforcement, what ever notary can decrypt
- extendable to threshhold proxy re-encryption
	- adds more entities to avoid single point of trust
	- at least k out of n (extreme n out of n) must agree to decrypt using own key

### Reducing time complexity
- run multiple buses per communication line 
- at stations messages can change buses so that they travel the shortest possible path
- next step in that is introducing busses that can cross lines to other networks
	- analogy to buses connecting places in a city and trains connecting cities
- increases communication complexity as we need to switch seats all the time
- works with minimal spanning trees

### Drunken Motorcyclist
- achieves sender/recipient anonymits
- imitates broadcast on a graph
- message is randomly forwarded along the graph
	- using invisible implicit addressing
- uses dummy messages 
	- to achieve unobservability
- strict synchronization (to avoid timing attacks)
- likely working on some peer2peer system

![[Pasted image 20240814143635.png]]
- message goes from A to random connected node
	- each node stores and forwards it
		- to have same time behavior for each node
	- no matter if it is the recipient or not
	- message runs as long as ttl != 0
		- once 0 it is dropped
- there is a certain chance, that the message never reaches the desired destination 
	- then needs some protocol when and how to resend