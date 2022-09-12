# Helios cryptography 

[^1]:
    [Helios documentation](https://documentation.heliosvoting.org/verification-specs/helios-v4)

[^2]: 
    This description is taken from [https://bensmyth.com/files/Smyth10-definition-verifiability.LNCS.pdf](https://bensmyth.com/files/Smyth10-definition-verifiability.LNCS.pdf)


- The election administrator sets up the election and generates a public - private key pair.
- The election has a set of trustees, each having a share of the private key but no-one holding the full private key.
- The public key is published.
- To cast a vote, the user runs a browser script that inputs her vote and creates a ballot that is encrypted with the public key of the election. The ballot includes a ZKP (Zero Knowledge Proof) that the ballot represents an allowed vote.
- The user can audit the ballot to check if it really represents a vote for her chosen candidate; if she elects to do this, the script provides her with the random data used in the ballot creation. She can then independently verify that the ballot was correctly constructed, but the ballot is now invalid and she has to create another one.
- When the voter has decided to cast her ballot, the voter’s browser submits it along with her pseudo-identity to the server. The server checks the ZKPs of the ballots, and publishes them on a bulletin board.
- Individual voters can check that their ballots appear on the bulletin board. Any observer can check that the ballots that appear on the bulletin board represent allowed votes, by checking the ZKPs.
- The server homomorphically combines the ballots, and publishes the encrypted tally. Anyone can check that this tally is done correctly.
- The server submits the encrypted tally to each of the trustees, and obtains their share of the decryption key for that particular ciphertext, together with a proof that the key share is well-formed. The server publishes these key shares along with the proofs. Anyone can check the proofs.
- The server decrypts the tally and publishes the result. Anyone can check this decryption.


There is also a good explanation in [Usability Analysis of Helios - An Open Source Verifiable Remote Electronic Voting System](http://static.usenix.org/events/evtwote11/tech/final_files/Karayumak7-8-11.pdf)


