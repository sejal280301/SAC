- going from broadcast to queries and keeping recipient anonymity
- and this is query and superpose
![[Pasted image 20240812160706.png]]

- users request rows/messages from databases
	- it shall not be visible which row they query in order to keep the requester anonymous

- message service is comprised of multiple instances of databases with replicated content
- and some requesting users that want to query messages from a certain row or ID
	- yey, it's inherently a distributed system
- superposing (überlagern) comes into play as soon as a client has requested information from all existing servers
![[private_message_service]]
- m servers with n replicated memory cells
- client send query vectors to s servers (1<s<=m)
	- query vector of length n with 1 for requested cell and 0 for not requested
		- e.g. 11101 -> all cells but 4th
	- 0 - s-1 = random vectors
	- s-th vector XOR of others
	- XOR all s vectors with the actual query vector 
		- e.g. 01000 for actually you want to receive the 2nd message
	- send vectors to s different servers (encrypted)
- servers fetch requested messages from cells
	- XOR them
	- send XOR result back (encrypted)
- client superposes results
	- XORs them
	- result is requested vector
	- because math works that way
- encryption of all messages is important because otherwise attacker/listener could obtain which row is requested or which row was returned by XORing requests or responses
- having less than s servers under control gives no information which cell was requested
	- there is a proof for that but I think the XOR thing is quite easy to understand it
	- the argument is that each of the single query vectors can be seen as the one that actually holds the information -> thus you can always see that one that the attacker has not as the relevant one
	- in essence you need all of them to reconstruct the requested cell
- why not just query all messages instead?
	- depends on the network scenario
	- may be beneficial in the right setting, apparently mobile is one of them
- how does a client know that there is a message for him?
	- good one!
## Improvements
### Save some traffic from client to databases
- before starting operation share PNRG seeds with servers
	- create random query vectors based on random number of PNRG for s-1 servers
	- create s-th query vector as usual
	- only send single query to s-th server
	- get responses from all s servers
	- still no one knows what was requested/responded without having control over all s servers
- drawback:
	- vectors aren't that random any more
		- unrestricted attacker could break the system
		- need to scale vectors and PNRGs to a size that attackers are busy for a while
### Save some traffic from databases to client
- still pre exchanged PNRGs as before and client only sends to a single s-th server
	- s-1 servers create their local XOR sums 
	- XOR them again with a locally created pseudo one time pad
	- send that (cell_1 XOR ... XOR cell n) XOR PseudoPad to next server
	- next XORs it in the calculation and so on
- client knows PseudoPadss because he knows PNRG seed
	- can XOR it out again when XORing all messages
- still only one message send and only one received
	- altough same amount of traffic for servers, now just between them
- one time pad is still one time
	- must be generated anew with each message
	- likely based on the same or another prng

![[private_message_service_improvements]]

- in both cases: how do the other servers, which are not queried, that there is a query?
	- sagt er jedenfalls im video nicht

- querying multiple messages would yield the XOR of multiple cells
	- not helpful except one of the messages is known
	- then you could XOR one away and get the other
## Thoughts about the whole scheme
- re-writable memory cell is basically an implicit address
	- whoever reads from there gets the message (anonymously)
	- knowing from which cell to read is up to the reader
	- sender is not anonymous
- reading messages from multiple cells works if you know the old content of one of the cells
	- e.g.
		- t0 read cell 1
		- t1 read cell 1 + 2
			- XOR with cell1 value -> cell2 value
		- t3 read cell 2+4
			- XOR with cell2 value -> cell4 value
		- ...
- improvement: XOR old message in cell with new messages
	- yields m1 XOR m2 which can be decoded to m2 by XORing m1
	- or with multiple messages
		- we received m1 XOR m2 with the first query
		- now only m1 is changed to m3 by XORing m1 with m3
		- we query the same cells again
		- we thus have m1 XOR m2 and \[m1 XOR m3 (=m3) XOR m2]
		- m1 XOR m2 XOR m1 XOR m3 XOR m2
			- ~~m1~~ XOR ~~m2~~ XOR ~~m1~~ XOR m3 XOR ~~m2~~ => m3
- in case there are multiple writers the system could break if values of multiple cells are altered
	- the system above assumes that nothing changed between reads
	- if it alters we'd need to get one of the single cells
		- yields that exact cell
		- and as well the one queried before as we can now successfully XOR again
- channels:
	- just put one message after another in the same cell
	- or in another to have a channel to another user
- reduce traffic if you're interested in exactly one memory cell
	- send the query vector to the server once
	- it just replies with the content for the same vector in each timestep
	- kinda subscribe to the cell
	- makes it a visible implicit address
### Multi-Cell Addresses
- so far the amount of cells equals the amount of implicit addresses
- if we use multiple cells for an address we can do better
	- image means to show that >n addresses can be done with the multi cell address
	- doesn't mean that we split cells
![[Pasted image 20240813114216.png]]
- write to all cells that compose an address
	- write random value in one cell
	- write the real message XOR'd with the random value in the other
	- reader XORs both to get the message
	- can also be adding and not XORing but all cells combined must make the real message
- for whatever reason we get $2^n -1$ addresses with that instead of $n$
	- n addresses with n cells is clear
	- with multiple cells for an address we need 2 cells per address
		- we pick 2 cells per address out of n possible cells
			- technically this should be like picking without repetitions
			- ${n\_cells \choose k\_address\_cells}$ --> ${4 \choose 2} = 6$    
- overlapping of cells means that there can be conflicts
	- in picture Addr1 and Addr3 will lead to conflicts in cell 1
	- in practice communication doesn't always happen at the same time
	- thus underprovisioning is okay
	- in case of conflicts a retransmit after some randomly chosen time is necessary
- there can be no flag that someone read a cell
	- whole point is that message server does not know when and if a cell was read
- as we had the option of directly querying the XOR of multiple cells above we can now directly get a complete message if we assume that we any way need to XOR two cells
	- we can with a matching set vector also request more than 2 cells or even sets of sets of cells
		- how sets of sets? mutliple set vectors?

### Hopping between cells
- reading is open for all
	- attacker can read all cells and thus measure how many and when cells were written
	- especially in the simple model (cell == address) we get the number of messages for that (unknown) recipient
- how to change that?
	- we work with PRNGs  with pre shared seeds with the servers again
	- message servers generate random number each time
		- write them to fixed address
		- a sender can read all of them and XOR them
	- all random numbers XOR'd give the cell used in next time slot
		- == new address for each time stamp for a recipient
		- recipient can calculate new address himself by knowing seed for PRNG
### Fault tolerance and modifying attacks
- no response or intentionally wrong result from server
	- ask different servers
	- set is not fixed and doesn't need to be as large as all existing servers
		- there should be a reserve
	- ask two disjunct sets of servers compare the results
	- in case of mistrust identify the cheater by asking enough different sets
- in case of altered messages add a MAC
	- again, search the cheater by asking different server sets
- searching the baddie is dangerous - you shall not reveal your cell and thus your interests
	- lay traps and act carefully
	- traps must be indistinguishable from real messages
	- you can't just shout out that something is wrong
		- but you can reveal to third parties how your traps look like and thus proof your search
## Living example - Vuvuzela
- google it if you find time