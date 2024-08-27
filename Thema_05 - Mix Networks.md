- yet another technology
	- different from 
- idea: provide unlinkability between incoming and outgoing messages
![[Pasted image 20240815151602.png]]
## Attacker Model
- control all network links
- manipulate all messages on links
	- delay
	- insert own
	- delete messages
- does not control **all** mixes
	- controlling n-1 of n mixes is fine
- all n can be dishonest but they must all be controlled by different attackers
	- also place them in different countries or juristrictions to avoid force by law

- still attacker shall not be able to correlate messages going in and out of a mix

## General idea
- use encryption to change how messages look like when going in a mix and out of a mix
- mix collects messages
	- reorders them
	- recodes them
	- forwards reordered and recoded
- properties of a mix service
	- uniform time behavior independent of message size
	- uniform incoming and outgoing message size
- need multiple mixes as one could correlate input and output
	- attacker would need to control all mixes on the path
	- one is not sufficient as input source is unknown

### Basic functionalities
- batch process messages
	- 
- discard replicas
	- avoids replay attacks
	- if all ins and outs to a mix are stored by an attacker and the decryption/re-encryption is deterministic we can just replay an old message and check which of the prior recorded outputs matches

### Message Format

`key_mix1(random, key_mix2(random, key_recipient(message)))`
- why the randomness?
	- to be able to send the same GET request without it being dropped as it was sent before
	- i.e. different looking cyphertext
	- also attacker could take output and re encrypt it with mixes pub key
		- could thus get the input and create the input/output correlation
		- observing attack completely
	- thus, each station needs to drop the randomness and not make it public
		- avoids that attacker can recompute input
### Which attack type is important to consider?
- (adaptive) chosen  plaintext
- (adaptive) chosen  cyphertext
	- this one is apparently the tough one
	- attacker can send some cyphertext and he'll see the decryption of that cyphertext
	- we need an algo that is resilient against that

### Which Crypto?
- asym for all mixes in the net
	- disadvantage: less performant, can't be information theoretic secure
- sym or asym for the first mix
	- sym is more performant and in principle allows inf theoretic security
	- but must then always be the start mix
![[mixes_encryption]]

### What happens in a single mix
![[Pasted image 20240815163313.png]]
- discarding discussed above
	- avoid replay attacks of copied messages
- waiting for sufficient messages
	- single message would be not enough to hide anything
	- why from sufficiently many senders?
		- n-1 attack or flooding attack
		- assume many attacker controlled stations sending to a mix
		- only one non-attacker controlled station sends
		- attacker knows output of mix as he created it prior
		- can thus correlate the input with the output message
	- need to have a way to differentiate senders
		- mixes down the line must be able to differentiate even if they just get messages from other mixes
	- also waiting inherently is slow
		- we may need to create and insert dummy messages
		- dummy messages are a problem as the last recipient will understand that this was a dummy
		- especially problematic if a recipient is malicious
			- can delete messages going into mix-net during communication
			- if other messages make it trough and you only get a dummy message
			- likely you deleted the message from the right com partner and identified him
## Mix Topologies
- always use multiples of the fuckers
	- how you build that human centipede is an option

![[Pasted image 20240815170801.png]]
- cascades
	- you just run along the fixed cascade of mixes
	- the whole cascade is either secure or it ain't
- free route
	- sender selected route trough the mixes
	- switching routes may lead to choosing only mixes that are controlled by an attacker
- assuming at least one honest route the cascade is always save
	- while the free route has a chance to take only malicious nodes
![[Pasted image 20240815172155.png]]
- restricted routes (last topology)
	- some nodes are fixed e.g. entry and exit
	- middle nodes are free to choose
	- kinda what TOR does

## Encryption schemes

### Sender anonymity
- direct re-encryption
	- each mix just decrypts his message and sends it forth
- indirect re-encryption
	- instead of just publicly encrypting the message for each mix
	- you add a sym key to the encrypted section and encrypt the actual message with that key
	- pubKey_mixX(symKey, next recipient, symKey(encrypted message for next recipient))
		- whatever the fuck A is
	- helps with efficiency as sym is easier
		- as each key for each package is newly generated no problem with that
		- assuming that message is larger then sym key and next recipient the efficiency gain helps

![[Pasted image 20240815173734.png]]
- guesstimation to letters:
	- M = Message
	- c = cryptKey
	- k = symKey
	- A = recipient

### Recipient anonymity
- now recipient must determine route to him
- creates anonymous return address
	- sender must combine it with his message and mixes bring it to recipient
	- like a envelope with an unreadable address that only many post men can read
	- uploads anonymous return address to some blackboard in an untracable manner
	- that is then his invisible implicit address
![[Pasted image 20240815181227.png]]
- the recipient creates as many sym keys as he want to have mixes on the way + 1 for the sender
	-  he encrypts the generated sym keys (+ I assume the next recipient) with the respective pub keys of the mixes / the sender
- this chain of encrypted sym keys + recipients is sent to the sender in some unobservable manner
- sender decrypts with his private key the sender sym key and the first mix to send information to
- in a header he sends the whole package generated by the recipient which is encrypted with the pub key of the mix, contains the sym key for the first mix and the remaining keys for the other mixes
- with that the sender also sends some Inhalt (I)
	- that is only encrypted with senders sym key so far
	- the mix applies the learned sym key it got from the header to the encryption
		- so it's double encrypted now
- the mix goes on and sends the message on to the next mix
	- that one again learns the sym key from the header
	- encrypts the inhalt again
	- forwards the inhalt to the next mix and also the header to the next mix
- this goes on until the last mix gets Rs address from the header
	- it applies the sym encryption a last time
- sends on
- R receives the message
	- has all sym keys and just applies them in reverse order to encrypt the original message

- why not just send all sym keys for all mixes to the sender?
	- the sender could just encrypt the message in reverse order and avoid the load on the mixes
	- but if then all mixes (as usual) decrypt everything the sender could spy on the line and would find his intiial encryption of his sym key going from some mix to some participant
		- that would end the recipient anonymity
	- doesn't work with the header content split as the sender does not know all sym keys the mixes add on the way
- but can't the recipient, based on the same argument see the message from the sender to the first mix?
### Sender and Recipient anonymity
![[Pasted image 20240815190021.png]]
- combine both prior approaches
	- recipient creates anonymous return address up until some mix on the road
		- sends anonymous return address to sender
	- sender uses sender anonymity scheme to send message until some mix
		- that last mix must then hand over to the last mix picked by the recipient
- recipient
	- recipient encrypts with mix asym keys backwards the created sym key and outmost with sender asym key
		- cs(ks, M4, c4(k4, M5, c5(k5)) )
	- sends envelope to sender
- sender
	- decrypts envelope
		- gains sym key for recipient
	- applies all sym keys to message
		- k1(k2(k3(ks(m)))) = msg
	- header is either added or sent separately
		- c1(c4(k4, M5, c5(k5, R))
	- applies pub encryption to sym keys for mixes of his choice
		- c1(k1, M2, msg)
- sends header and content to first mix
	- mix decrypts content, gets what to send on
	- decrypts content by one
- yada yada till last chosen sender mix
	- decrypts, finds next mix - first of recipients choice
	- forwards header and ks(m)
- first recipient mix decrypts sym key
	- encrypts! ks(m)
	- sends remaining header further
- yada till last mix
	- encrypts message last time
	- sends remaining header to receiver
- receiver encrypts everything
- no one knows full path, only from first chosen to last chosen mix + 1 the one it came from/went to


### Keeping message size while removing keys
- taking out the sym key leaves quite some room in the package
- in order to obtain equal input and output size this room must be filled
- also room must need to be filled with reordering
	- all mixes should find their key and info in the beginning of the package
	- else he'd need to know which mix in the row he is
- thus, adding random padding happens at the end of the message
- also, everything behind the data for a single mix should not be identifiable as random or real message
	- thus, also just encrypt the random content
	- applies only for header, not for message content
- if header and content are in same message
	- how to know where content starts?
	- we don't we just apply another scheme where we move that as well
- that other scheme where we move the content as well
	- works only with special symmetric encryption
	- those where it does not matter where you apply encryption or decryption to plaintext in order to achieve a secure cyphertext
	- then you just append to the end
	- the last mix needs just forwards the remainder that has then the message content at the beginning of the message
- whatever remains and is not just randomly added is re-encrypted
	- works with block and stream cyphers

## ISDN telephone mixes

## Tor
- attacker model differs from assumed mixnet attacker model
	- attacker is only assumed at beginning XOR end, not both
	- if he is at start and end he can correlate traffic amounts
- hidden services are means of recipient anonymity
- tor generally provides sender anonymity
