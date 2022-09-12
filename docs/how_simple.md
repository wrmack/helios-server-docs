# Simple encryption model

A simple encryption model for conducting an election might go something like this:

- Election administrator sets up the election and creates a public - private key pair.
- Election administrator publishes the public key.
- Each voter encrypts their vote with the election public key and sends the encrypted vote to the election administrator.
- When the election closes, the administrator decrypts all votes using the private key and tallies the votes.
- The election administrator publishes the result.

Communication of the vote would likely be over TLS and so it would be encrypted in transmission anyway.  The only security added by this model is that when it is received and stored on the server it is encrypted. The model has other problems.

# Problems

- the election administrator is able to decrypt votes and may be able to determine how a particular voter voted (ballot privacy breached)
- the voter does not know whether their vote was correctly encrypted (cast-as-intended cannot be checked)
- the voter is not able to verify that the election outcome correctly represents their own vote (recorded-as-cast and tallied-as-recorded cannot be checked)