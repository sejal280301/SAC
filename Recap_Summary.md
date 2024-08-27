## Recipient protection
- two ways thought to protect recipients
	- broadcast
	- private message service
- broadcast
	- known sender sends message to all possible recipients
	- can be based on broadcast medium like WiFi or Bluetooth but can also be done in blackboard style
		- send to public visible instance from where on it is forwarded
	- point is that recipients are unknown
		- an attacker can not judge about whether someone was the intended recipient for a message as everyone gets it
		- as long as addressing is invisible -> no public visible addressing method in messages but only recipients can understand that message is for him
		- implicit addressing makes it better, e.g. encrypt message with key of recipient -> only he can encrypt and understand that message is for him
- private message service
	- we change the paradigm
	- no single sender pushing messages to multitude of recipients but recipients pull from database
	- use the superposition instrument of pulling more then necessary to distill the information they want out of it
		- also make it impossible for controller of less than all servers they interact with to get what they queried
	- 
## Sender protection
- 