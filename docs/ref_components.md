# Components

### Django installed apps
HelioSolid is an application which has been deployed to a server.  The application uses the Django web framework for serving pages and storing data.  The Django application comprises three "installed apps":

* server_ui
* helios_auth
* helios

#### server_ui
The server_ui app presents the initial page with:



#### helios
The helios app presents all pages relating to an election. It holds the list of registered voters for an election. It stores all encrypted ballots once they have been retrieved from voter's pods. It tallys the vote once all ballots have been retrieved. 

#### helios_auth
The helios_auth app handles authentication. 

### Voting booth
Once an admin has uploaded a csv file of voters the admin will cause an email to be sent to voters which contains their login name and password and an url to the election.  When a voter clicks on the url the server presents the voter with the voting booth which is updated by javascript as the voter works through their voting selections.  The vote is encrypted in the browser.  There are no further network requests to the server until the voter decides to cast their vote.  The voting booth is decoupled from the server.



