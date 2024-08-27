## PETs - Privacy enhancing technologies
- Opacity Tools
	- information suppression
	- anonymization
	- pseudonymization
	- obfuscation
- Transparency enhancing tools (TETs)
	- inform user about data collection and purpose
	- inform about impact of data collection
		- basis for "informed consent"
	- enable checks whether collection is legal
	- techniques
		- secure logging
		- audits
		- quality seals
		- policies
## Protection Goal Anonoymity
- ensures that a user can use a resource/service without disclosing his/her identity. Not even communicants can discover the others identity.
- the state of not being identifiable within the a set of subjects, the anonymity set.
	- the larger the set and the more uniform the (sending/receiving) behavior, the better the anonymity.
	- number of users has a direct impact on the anonymity -> the more the merrier

## Protection Goal Unobservability
- ensures that a user can use a service/resource without others being able to see that it is being used. Uninvolved parties can see neither sending nor receiving.
- of two or more items of interest (IOIs: subjects, messages, actions, etc.) from an attackers perspective means that an attacker can not sufficiently distinguish whether these IOIs are related or not within that system.
- anonymity in terms of unlinkability: 
	- unlinkability between an identity(subject) and an IOI (action, message, data reord, etc.)
- Correlations between protection goals:
![[Pasted image 20240811155736.png]]
- hiding: ensures the confidentiality of communication between participants -> no one except the participants can discover the existence of confidential communication.

![[Pasted image 20240811160516.png]]
- general problem: we can be observed from some point of view
	- end2end encryption helps us with observability trough interceptors
	- link encryption helps with interceptor reading addresses on the line
	- switches and exchanges on the line still get info who with whom
	- other part is that we never know what the other end saves about us
		- profiling
		- ad networks
		- relaying data to third parties
- aim is to build a system were we can anonymously communicate without someone being able to see who were talking what with
	- Broadcast, Query and Superpose, Ring, DC-Net, Mix-Nets, 

![[Pasted image 20240811184719.png]]
- satellites - broadcast to everyone, undetectable who receives it
![[Pasted image 20240811184839.png]]
- changing to routed networks allows profiling and registration of who sees what
	- in general the method of requesting something allows it to log exactly that request
- letter post vs. email
	- no one enforces full traceability - altough weaknesses are known
	- email is completely traceable via all routes be it sending/receiving server or endusers
- newspaper also replaced by electronic paper sooner or later
	- steering of interesting topics based on readers demands
	- again traceability
- cloud and smart devices are generally the enemy of privacy
	- track whatever
	- send to whomever
	- profile from home over car to health
## Types of data
- data without relation to individuals
	- simulation data
	- data from experiment measurements
- data with relation to individuals
	- types: content and metadata
	- revelation: consciously or unconsciously
## Notions of privacy
- right to be let alone
	- based on new trend photography new right created that defines that people have a right to be let alone
		- not be photographed or some other way their image and right taken from them
- data protection
	- collect and process data fairly and lawfully
	- keep it safe and secure
	- purpose binding
		- use only for what the user consented
		- specified explicit purposes
		- also disclosure only in those ways
	- data minimization
		- do not obtain what is not needed - adequate and relevant collection
		- retain no longer then necessary
	- transparency
		- inform about collection and purpose
		- inform about processing, storing and forwarding 
	- user has access rights to own data
		- also correction and deletion
- contextual integrity
	- pays attention to appropriateness and distribution, both assessed as privacy violations
	- violation of appropriateness
		- usage of information in context B while obtained in context A
		- even if A was public
		- thus, context defines whether information revealing is appropriate
	- violation of distribution
		- context defines which flow of information is fine
		- violation appropriate flows is evil...?
![[degrees_of_anonymity]]
- with sender anonymity
	- absolute anonymity: unobservability
	- beyond suspicion: not any more likely than any other subject
	- probable innocence: no more likely to be sender or not to be sender
	- possible innocence: nontrivial probability that someone else is sender

- protecting traffic data with mechanisms outside the network
	- usage of public terminals
		- cumbersome
	- temporally decoupled processing
		- makes real time communication impossible
	- local selection
		- download everything choose once you have it
		- transmission performance may be bad or just too much data to get everything
		- extra fees for usage of humongous amounts of data
	- protection inside network is worth evaluating more
## Attacker Model
- somehow he just says that given unobservability an attacker learns nothing by looking at the communication
- also we have a chance between 0 and 1 that our event happened and was observed