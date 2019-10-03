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
When a voter clicks on the button to vote in the chosen election, the server presents the voter with a page which is updated through javascript provided with the page.  There are not further network requests to the server until the voter decides to cast their vote.  The voting booth is decoupled from the server.



