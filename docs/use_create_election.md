# Election admin creates an election

[^1]:
    [https://heliosvoting.org/privacy](https://heliosvoting.org/privacy)



To create and administer an election, you will need to log in using Google, Facebook, Yahoo, or Twitter. Helios queries and retains only the basic information needed to authenticate you the next time around: your name, email address, and user ID. Helios will use this information to message you when your election requires attention.[^1]

As an election administrator, you have the power to designate who can vote, when the election starts, when the election stops, and when the results are released. Other than that, you have no power beyond what voters in your election have. This is by design.[^1]

On the home page for Helios, after logging in, click on `create election`.

## Create election

Complete the fields on the form and press `Next >>`.  Once the election is added it will be possible to come back and edit it up until you choose to freeze it.

| Field | Comment |
|-------|---------|
|Short name             | no spaces, will be part of the URL for your election, e.g. my-club-2010 |
|Name                   | the pretty name for your election, e.g. My Club 2010 Election |
|Description            | appears on main page for this election / referendum |
|Type                   | election / referendum; ??? |
|Use voter aliases      | If selected, voter identities will be replaced with aliases, e.g. "V12", in the ballot tracking center |
|Randomize answer order | enable this if you want the answers to questions to appear in random order for each voter |
|Private?               | A private election is only visible to registered voters.|
|Help email address     | An email address voters should contact if they need help. |
|Voting starts at       | UTC date and time when voting begins |
|Voting ends at         | UTC date and time when voting ends   | 

Pressing `Next >>` takes you to the main page for the election.

## Election main page

There is a statement about whether this election is featured on the front page.  In order to feature elections on the front page the user field `admin_p` in the helios_auth_user database table needs to be set to True.  Connect to the database in a termimal and run the SQL query:

```
UPDATE helios_auth_user SET admin_p = 't' WHERE user_id = [your_user_id];
```

From this page you can set up:

* the questions for voting on
* voters and ballots
* trustees

### Questions

For each 'question' a voter may vote for one or more 'answers'.  A question might be 'Which two candidates should be elected?' and the available answers might be similar to candidate-one, candidate-two, candidate-three, candidate-four.  You can set how many answers may be chosen and whether the results should be expressed in absolute number of votes or relative numbers of votes (percentages). You can associate a link with each answer (for example a link to further information about a candidate).

### Voters & ballots

You may select one of:

* anyone can vote, a voter simply needs to log on, eg using Google authentication
* only the listed voters can vote

If you choose the second option, you need to upload a list of voters contained in a csv file.

### Trustees

A trustee uses a private key to decrypt the result of the tally.  Helios is automatically a trustee.  It is not necessary to add further trustees, but if you do, it is important they retain their secret keys.  All secret keys will be required to decrypt the result.

### Freeze ballot and open election

Once you have set up the questions made your selection in the voters & ballots section you can then freeze the election and open it for voting.  If you uploaded a list of voters, you can, in the Voters & ballots section, send an email to all voters giving each of them their Voter ID and password.



<!-- 

See for good description:
https://github.com/python/psf-election 

-->