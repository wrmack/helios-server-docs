# End-to-end verifiability

[^1]: 
    [End-to-end verifiability](https://arxiv.org/ftp/arxiv/papers/1504/1504.03778.pdf) <span style="font-size:10pt;line-height:1.0"> *Josh Benaloh, Microsoft Research. Ronald Rivest, MIT.  Peter Y. A. Ryan University of Luxembourg.  Philip Stark, UC Berkeley.  Vanessa Teague, University of Melbourne.  Poorvi Vora, George Washington University. (2014)*</span>

[^2]:
    [An Overview of End-to-End Verifiable Voting Systems](https://arxiv.org/pdf/1605.08554.pdf) <span style="font-size:10pt;line-height:1.0">*Syed Taha Ali and Judy Murray. (2016)*</span> 

Voters and candidates should not have to blindly trust that electronic election technology works.  It might be compromised by an attacker or it might be incorrectly coded.  How do voters and candidates know the result is correct?  End-to-end verifiability (E2E-V) provides for voters and candidates to verify an election result is correct.[^1] [^2]  

Verifiability is generally exhibited by:

* **Individual Verifiability**: a voter can verify that her vote is included in the set of all cast votes.
* **Universal Verifiability**: an observer can verify that the tally has been correctly computed from the set of all cast votes.

Verifiability can be acheived as follows:

* **Cast As Intended**: voters make their selections and, at the time of vote casting, can get convincing evidence that their encrypted votes accurately reflect their choices;
* **Recorded As Cast**: voters or their designees can check that their encrypted votes have been correctly included, by finding exactly the encrypted value they cast on a public list of encrypted cast votes; and
* **Tallied As Recorded**: any member of the public can check that all the published encrypted votes are correctly included in the tally, without knowing how any individual voted.





