# Terms

### ballot
A ballot is a document setting out the choices the voter is entitled to make.  It is presented to the voter in the voting booth.  The voter makes their choices and saves the encrypted ballot to their pod.  The application automatically sends a notification to the election admin for that election. 


### election
An election which is created by an election admin.  Information relating to the election is stored on a database. The information includes:

* all voter webids
* encrypted ballots, once retrieved from pods


### election admin
A person entitled to administer an election. An election admin may create an election.  An election admin who creates an election:

* will receive notifications of ballots saved to voters' pods
* may retrieve ballots from pods following notification
* may tally all retrieved ballots
* may announce the result of the tally

A person is entitled to administer an election if ....


### user
The currently logged in user who may be an election admin and / or a voter.  

Or: the currently logged in user who will be a voter and may be an election admin.

### voter
A person who is registered to vote in the current election.

### voting booth
