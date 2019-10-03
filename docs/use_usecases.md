# Use cases
## Election admin creates an election

## Person registers as voter

## Voter votes
* must be logged into a solid pod
* voting booth decoupled from server
* entirely in browser
* ballot is encrypted

## Voter saves ballot to pod

* saved in encrypted form
* voter fully logged out to avoid misuse by subsequent user
* solid authentication is not for SSO purposes
* notification automatically sent to pod of election creator

## Election admin retrieves all ballots 

* ballots saved in database

## Election admin tallies ballots

* tallied as per norm for helios
* no trustees

## Election admin announces results

## Verifier verifies all ballots were counted

* uses unique computer or device
* checks pod of every registered voter
* number of pods with a ballot in pod should match number of notifications received and number of votes counted

## Verifier verifies ballots counted correctly

* unique computer or device
* assuming above verification was successful, uses notifications received by election creator
* repeats the count conducted by election creator 