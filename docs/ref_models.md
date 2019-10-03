# Models

*"A model class represents a database table, and an instance of that class represents a particular record in the database table."*
 Django docs 

## helios

### Election class

| Field                       | Type           | Default                                                                                        | Comment      |
|-----------------------------|----------------|------------------------------------------------------------------------------------------------|--------------|
| uuid                        | Char           |                                                                                                |              |
| datatype                    | Char           | legacy/Election                                                                                |              |
| short_name                  | Char           | unique=True                                                                                    | Set by admin |
| name                        | Char           |                                                                                                | Set by admin |
| election_type               | Char           | choices=<br />[('election', 'Election'),<br />('referendum', 'Referendum')],<br />default=election |              |
| private_p                   | Boolean        | False                                                                                          |              |
| description                 | Text           |                                                                                                |              |
| public_key                  | LDObject       | null=True                                                                                      |              |
| private_key                 | LDObject       | null=True                                                                                      |              |
| questions                   | LDObject       | null=True                                                                                      |              |
| eligibility                 | LDObject       | null=True                                                                                      |              |
| openreg                     | Boolean        | False                                                                                          |              |
| featured_p                  | Boolean        | False                                                                                          |              |
| use_voter_aliases           | Boolean        | False                                                                                          |              |
| use_advanced_audit_features | Boolean        | True                                                                                           |              |
| randomize_answer_order      | Boolean        | False                                                                                          |              |
| cast_url                    | Char           |                                                                                                |              |
| created_at                  | DateTime       | auto_now_add=True                                                                              |              |
| modified_at                 | DateTime       | auto_now_add=True                                                                              |              |
| frozen_at                   | DateTime       | None, null=True                                                                                |              |
| archived_at                 | DateTime       | None, null=True                                                                                |              |
| registration_starts_at      | DateTime       | None, null=True                                                                                |              |
| voting_starts_at            | DateTime       | None, null=True                                                                                |              |
| voting_ends_at              | DateTime       | None, null=True                                                                                |              |
| complaint_period_ends_at    | DateTime       | None, null=True                                                                                |              |
| tallying_starts_at          | DateTime       | None, null=True                                                                                |              |
| voting_started_at           | DateTime       | None, null=True                                                                                |              |
| voting_extended_until       | DateTime       | None, null=True                                                                                |              |
| voting_ended_at             | DateTime       | None, null=True                                                                                |              |
| tallying_started_at         | DateTime       | None, null=True                                                                                |              |
| tallying_finished_at        | DateTime       | None, null=True                                                                                |              |
| tallies_combined_at         | DateTime       | None, null=True                                                                                |              |
| result_released_at          | DateTime       | None, null=True                                                                                |              |
| voters_hash                 | Char           | null=True                                                                                      |              |
| encrypted_tally             | LDObject       | null=True                                                                                      |              |
| result                      | LDObject       | null=True                                                                                      |              |
| result_proof                | jsonfield.JSON | null=True                                                                                      |              |
| help_email                  | Email          | null=True                                                                                      |              |
| election_info_url           | Char           | null=True                                                                                      |              |
| voter_webid                 | Char           | null=True                                                                                      |              |

### VoterFile class

| Field                  | Type       | Default           | Comment           |
|------------------------|------------|-------------------|-------------------|
| voter_file             | File       | null=True         | This is a comment |
| voter_file_content     | Text       | null=True         |                   |
| uploaded_at            | DateTime   | auto_now_add=True |                   |
| processing_started_at  | DateTime   | null=True         |                   |
| processing_finished_at | DateTime   | null=True         |                   |
| num_voters             | Integer    | null=True         |                   |
| election               | ForeignKey |                   |                   |


### Voter class

| Field          | Type       | Default   | Comment           |
|----------------|------------|-----------|-------------------|
| uuid           | Char       |           | This is a comment |
| voter_login_id | Char       | null=True |                   |
| voter_password | Char       | null=True |                   |
| voter_name     | Char       | null=True |                   |
| voter_email    | Char       | null=True |                   |
| alias          | Char       | null=True |                   |
| vote           | LDObject   | null=True |                   |
| vote_hash      | Char       | null=True |                   |
| cast_at        | DateTime   | null=True |                   |
| election       | ForeignKey |           |                   |
| user           | ForeignKey |           |                   |


### ElectionLog class

| Field    | Type       | Default           | Comments          |
|----------|------------|-------------------|-------------------|
| log      | Char       |                   | This is a comment |
| at       | DateTime   | auto_now_add=True |                   |
| election | ForeignKey |                   |                   |


### CastVote class

| Field                       | Type             | Default                | Comments          |
|-----------------------------|------------------|------------------------|-------------------|
| vote                        | LDObject         |                        | This is a comment |
| vote_hash                   | Char             |                        |                   |
| vote_tinyhash               | Char             | null=True<br />unique=True |                   |
| cast_at                     | DateTime         | auto_now_add=True      |                   |
| quarantined_p               | Boolean          | False                  |                   |
| released_from_quarantine_at | DateTime         | null=True              |                   |
| verified_at                 | DateTime         | null=True              |                   |
| invalidated_at              | DateTime         | null=True              |                   |
| cast_ip                     | GenericIPAddress | null=True              |                   |
| voter                       | ForeignKey       |                        |                   |

### AuditedBallot class

| Field     | Type       | Default           | Comments          |
|-----------|------------|-------------------|-------------------|
| raw_vote  | Text       |                   | This is a comment |
| vote_hash | Char       |                   |                   |
| added_at  | DateTime   | auto_now_add=True |                   |
| election  | ForeignKey |                   |                   | 

### Trustee class

| Field              | Type       | Default   | Comments          |
|--------------------|------------|-----------|-------------------|
| uuid               | Char       |           | This is a comment |
| name               | Char       |           |                   |
| email              | Email      |           |                   |
| secret             | Char       |           |                   |
| public_key         | LDObject   | null=True |                   |
| public_key_hash    | Char       |           |                   |
| secret_key         | LDObject   | null=True |                   |
| pok                | LDObject   | null=True |                   |
| decryption_factors | LDObject   | null=True |                   |
| decryption_proofs  | LDObject   | null=True |                   |
| election           | ForeignKey |           |                   |

## helios_auth

### User class


| Field     | Type                | Default   | Comments          |
|-----------|---------------------|-----------|-------------------|
| user_type |                     |           | This is a comment |
| user_id   |                     |           |                   |
| name      |                     | null=True |                   |
| info      | jsonfield.JSONField |           |                   |
| token     | jsonfield.JSONField | null=True |                   |
| admin_p   | Boolean             | False     |                   |
