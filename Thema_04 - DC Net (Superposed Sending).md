## Dining Cryptographers
- Dining Cryptographers  = DC
- meant to protect the sender
- usually also provides recipient anonymity
- scenario to start with are the dining cryptographers 
	- three cryptographers at dining table
	- waiter informs them that bill was paid
		- either NSA paid 
		- or one of them and he shall stay anonymous
	- they want to make sure on of them paid but keep the anonymity up
	- thus they need a protocol with which they can ensure that one of them paid and not understand who
 
![[dining_cryptographers]]
- setup for DC-Net
	- exchange shared key between each pair of cryptographers without the third one seeing it
		- e.g. flip coin behind menu for 1 bit key
- now everyone announces publicly
	- if he paid: **negated** XOR of both keys
	- if he did not pay XOR of both keys
- XORing all public announcements gives 0 or 1
	- 0 - NSA paid and no one
	- 1 - A cryptographer paid


### Use it as a network
- create larger keys and pre share them between participants
- one of network participants picks a message 
	- XORs the message with all keys
- others don't send a message at the same time -> message is all 0s
	- still XOR their message with all keys they have
- everyone broadcasts their XORed message
- XORing all messages yields the one real sent message
	- all keys XOR away
	- messages that are all 0 XORs away
	- only one message with not just 0s remains
- if someone sends a message at the same time the whole scheme dies
	- except you can handle XORing one away

### Attacker model
- can listen on all links
- key bits must be truly random and used only once
- attacker can never know whether message is dummy message
	- or real message or multiple messages
	- when only listening on lines and not knowing the keys

### Extension to abelian groups
- properties of abelian groups
	- there is a neutral element
		- A o 0 = A
	- there is a inverse element
		- A o -A = 0
	- commutative 
		- A o B = B o A
- instead of just XORing the keys one side needs to use the normal key
	- the other side needs to use the inverse key
	- side -> A, B sharing one key; A is one side, B the other
		- for binary XOR numbers are self inverse, thus we don't do anything
	- since we're abelian we are commutative and thus it does not matter who uses what
- however, computers work with binary
	- we thus don't need hex or anything else then binary

### More capable attacker
- we don't need a fully connected key-graph to have a working DC-net
	- but it's better to have as many keys exchanged as possible
- if our attacker can be part of the network 
	- and encircles a user
	- he can decrypt a message of the encircled station as he holds all used keys
- encricling becomes harder the more keys a station has exchanged
	- attacker needs them all
![[dc-net_attacker_inside]]

### Topologies
- Key topology
	- from a fully graph to a very sparsely connected graph
- superposition topology
	- where happens the adding of local sums?
	- locally at each station and all just publish their messages
	- or at some server were everyone just adds his local sum and forwards the result
		- e.g. A -> LS_a -> B -> LS_a XOR LS_b -> C -> LS_a XOR LS_b XOR LS_c -> A, B
- transmission topology
	- who sends what from where to where
	- independent from superposition and key topology
	- A could send via B or directly to the direct superposition entity
		- whyever it'd do that
		- B could also sum up his and As sum before forwarding to save a message
- transmission and superposition topology should match
	- each station is then free to decide about key exchange with the other stations
### Anonymous random access control
- we're using a reservation scheme that allows all participants to announce that they want to send but no one to understand who sends when
	- for that we use reserveration frames next to message frames and introduce something like super rounds
	- each station needs to announce in which round it wants to send
		- e.g. decide on random basis which round shall be taken
	- each super round starts with sending a reservation frame that contains as many entries as there will be rounds
	- for each entry > 1 sending makes no sense because multiple stations would want to send and would have collisions
	- for each 0 entry no one wants to send
	- for each entry = 1 one station sends
		- and it is not clear who
- after reservation round the actual messages are sent based on who sent what and knows what

#### Reservation Frame
- e.g. 03011
	- five rounds
	- no one in the first round, three in the second, no one third, one in last and last but one
- creation of reservation frame
	- each station sends in the style of the DC-net his desire for his reservations
	- e.g. 
		- station 1: 00000 XOR all keys
		- station 2: 01000 XOR all keys
		- station 3: 01001 XOR all keys
		- station 4: 01010 XOR all keys
	- all frames are added up and calculated mod x, wher x >= amnt participants
		- sum:         03011
		- sum is reservation frame
		- if x is smaller then group size 1 appears e.g. if all group members want to send
			- or make up some scenario where mod x gives 1 which is then misinterpreted
#### Problems with reservation scheme
- malicious station sends ones in reservation frame all the time
	- just DoSes the communication
	- and no one can find out that I'm blocking everything since this is anonymous
### Superposed Receiving - Global collision resolution
- if you know the global sum and n-1 of the local sums you can calculate the n-th sum
	- because we have this additive behavior
- even if we have collisions it is not complete garbage
	- would still be the XOR of m1 and m2 as everything else cancels out
	- and if the two stations just XOR their m1 or m2 "away" both senders know the message of the other
- thus, we can send multiple messages at the same time
	- search term: network coding use linear combinations of messages
- in pairwise communication (pairwise superposed sending) both sending at the same time is fine as they can subtract their message
	- in larger groups (global superposed sending) there is at least an added benefit of keeping what was sent as we can subtract from that later on
	- that is the large calculation thingy we did
- making this in a controlled fashion allows us to have a better medium access control protocol

#### Change the message format
- so far we just had a payload (the XOR of message and keys)
- now we add a counter
	- with some overflow area
- and an overflow area for the message we now want to add up
![[Pasted image 20240814171014.png]]
- uses the mean of all messages to resend those that were not understood

![[dc-net_global_superposed_sending_collision_resolution]]
- start by calcing the mean of all received values
	- if counter > 1
		- all stations resend the messages with value <= mean
	- repeat until counter reaches 1
		- you got the first message
	- build a global virtual sum by subtracting the first real message from the last received global sum
		- build the mean again
		- all stations <= mean resend
		- do until counter = 1 is reached
	- subtract from last (virtual) global sum
		- if 1, done
- done with both parts of tree
- in case of two times exactly the same value you'll receive the exact same global sum and counter again
	- if so, just divide by 2 and be done
	- or divide by the counter if more then 2 times the same message
![[Pasted image 20240814173111.png]]

#### Observations
- cool thing is that this works in as many messages as we want to decrypt messages
	- but without the reservation scheme and by just resolving collisions
- message arrive ordered in ascending values
	- this can be good
	- if used properly
	- otherwise it's a fact of the protocol

### Why does DC-net achieve sender anonymity
- Vernam-Cypher (OneTime Pad) and DC-Net are somewhat analogous
	- but is it the same?
		- almost
	- vernam - we xor message with same length unique bitsequence
	- dc - we xor the message with many keys
		- completely new keys in each round and thus for the next message
	- still, our key is used twice, once in each station
		- vs. the **One**Time Pad where each key is really only used once
		- and technically again when decrypting the message

#### Proof by Induction that DC-net of arbitrary number is secure
- proposition: if an attacker listens to all Outputs (O) of all connected stations (S), each having their shared keys (K) kept secret, learns nothing more then the sum of all messages (M)
	- in case of one single message he learns that message but not who sent it
	- in case of collision he'll learn the sum of the messages
- induction step from m-1 -> m
	- since m=1 is kinda pointless - he'd now the sender (and receiver) by default
- start with a network that is fully connected
	- add a new station that is connected to exactly one station (minimally connected)
- und das ist hier dann dieses lernen auf Lücke
	- wir gehen von m-1 -> m. deswegen fangen wir mit der folie für m an und packen die für m-1 dahinter


### Defense against modifying attackers
- problem 1: attack on recipient anonymity
	- attacker sends messages only to some users
	- if answer received addressee was in that set of users
- problem 2: attack on availability
	- if someone sends corrupted messages those must be investigated
	- meaningful messages, however, shall not be investigated

#### Attacking recipient anonymity
- we use broadcast in order to achieve recipient anonymity
	- everyone gets the global sum via all local sums
- thus, we can do the same as with every broadcast
	- we slim the anonymity set by just sending to some stations and not all
	- if replyer is in slimmed set he's part of that
	- go on slimming that further down
- e.g. 
	- global sum is calculated at one central station
	- attacker is somehow capable of manipulating the message on the line
	- he changes the output of the central station towards some station(s)
	- if a station replies it was not the one which received a manipulated message
- does encryption help?
	- partially
	- attacker can't read any longer whether a message is a response to a request
	- special part of being a participant in broadcast from point of view of the attacker
		- he gets all messages
		- he can send messages
		- encryption is only efficient against outsiders
		- if the attacker is part of the network (especially the DC net) he can learn who responds if he just removes the forwarding to one station
			- e.g. 3 stations, 1 is malicious
			- stops message to first other station
				- no response (visible because part of net) -> recipient is not that station
			- stops next message to second station, checks again
	- depends on the transistion topology quite heavy here
	- works also with public implicit invisible addresses
		- public key of drug dealer known + info that send message in dc net x
		- police does so, blocks forwarding to n-1 stations all the time
		- when it gets response then it found the station that is the drug dealer
		- i.e. knowing the invisible implicit address does not mean to know who the other party is
			- which kinda is the point of anonymity...eh?
- what to do against it?
	- if ever something went wrong we must hinder all stations to use the DC net further
		- DC-net should only output random data from there on
		- also only breaks the DC net and no off-net communication
	- so how do we detect it?
		- general observation in DC nets
			- if keys don't cancel out our global sums are random gibberish
		- by modifying the global sum creation for the next round based on the prior round
			- include the global sum in generation of the keys for the next round
	- to generate keys we add
		- sum up **all** prior received globals sum
		- multiply with random value
		- add all that to the freshly generated key with the other station
		- so you end up with different keys if history was changed or if someone received some wrong global sum
	- why whole history?
		- if only last round is taken one round is gibberish
		- that gibberish is then base for next round
		- as everyone has the same gibberish this is a valid input for the next round
		- thus we only destroyed one round
		- looking back at the initial attack we cannot be certain that our victim replies in the next round and not just in 5 rounds 
			- thus attack could still be effective
- as we always start with the last round in the calculation we need all rounds and always start from scratch - how to do that easier?
	- we calculate the key only based on the last s rounds
	- all senders send a dummy message or a real message in all s rounds
	- they check whether the global sum actually reflects what they sent

### Defending against DoS
- defender can always send message to scramble reception
- we want to punish the attacker at least
	- technically you can reveal who sent bullshit by revealing all keys that were used
		- only the keys, not the messages
	- would need the one who sent to call out received bullshit because the global sum ain't his message
		- but attacker could always say that he sent and global sum is false
		- thus would learn the keys and could read the message
		- and also who sent the message - violates sender anonymity
	- if sender stands up and says that things are odd he implicitly says that he is the sender
		- gives up anonymity
	- we need a second mode of operation to find out where the error comes from
		- if you figured that something was odd start sending random messages
		- if messed up again - start revocation process
			- you don't reveal you anonymity if message does not identify you
			- although you give up the message origin
	- anonymous trap protocol
		- from time to time send random messages as traps
			- if modified just shout out
		- attacker still has a certain chance of disturbing real communication
		- still the same problem. one person needs to shout out
			- how to prove that you actually sent the message?
			- add a reserveration blob
				- who reserved a blob sends a secret and holds that
				- in order to later claim that my message is broken I need that secret to prove that this was actually my reserved block
				- thus, prove that you sent, not just claim it
	- 