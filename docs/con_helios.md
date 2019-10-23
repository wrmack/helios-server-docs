# How Helios works. 

[^1]:
    [Helios documentation](https://documentation.heliosvoting.org/verification-specs/helios-v4)

[^2]: 
    This description is taken from [https://bensmyth.com/files/Smyth10-definition-verifiability.LNCS.pdf](https://bensmyth.com/files/Smyth10-definition-verifiability.LNCS.pdf)



(A general description is provided in [Wikipedia](https://en.wikipedia.org/wiki/Helios_Voting).)

Helios is a truly verifiable voting system, which means that:

* Alice can verify that her vote was correctly captured,
* all captured votes are displayed (in encrypted form) for all to see.
* anyone can verify that the captured votes were correctly tallied.[^1]

An election is created by naming a set of trustees and running a protocol that provides each of them with a share of the secret part of a public key pair. The public part of the key is published. Each of the eligible voters is also provided with a private pseudo-identity. The steps that participants take during a run of Helios are as follows.[^2]

1.  To cast a vote, the user runs a browser script that inputs her vote and creates a ballot that is encrypted with the public key of the election. The ballot includes a ZKP (Zero Knowledge Proof) that the ballot represents an allowed vote (this is needed because the ballots are never decrypted individually).
2. The user can audit the ballot to check if it really represents a vote for her chosen candidate; if she elects to do this, the script provides her with the random data used in the ballot creation. She can then independently verify that the ballot was correctly constructed, but the ballot is now invalid and she has to create another one.
3. When the voter has decided to cast her ballot, the voter’s browser submits it along with her pseudo-identity to the server. The server checks the ZKPs of the ballots, and publishes them on a bulletin board.
4. Individual voters can check that their ballots appear on the bulletin board. Any observer can check that the ballots that appear on the bulletin board represent allowed votes, by checking the ZKPs.
5. The server homomorphically combines the ballots, and publishes the encrypted tally. Anyone can check that this tally is done correctly.
6. The server submits the encrypted tally to each of the trustees, and obtains their share of the decryption key for that particular ciphertext, together with a proof that the key share is well-formed. The server publishes these key shares along with the proofs. Anyone can check the proofs.
7. The server decrypts the tally and publishes the result. Anyone can check this decryption.


There is also a good explanation in [Usability Analysis of Helios - An Open Source Verifiable Remote Electronic Voting System](http://static.usenix.org/events/evtwote11/tech/final_files/Karayumak7-8-11.pdf)


