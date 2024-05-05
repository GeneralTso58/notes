# AWS access control
AWS uses IAM web service to control access permissions of servcies and accounts
When a 
## IAM users
- username and password to sign in
- A primary use for IAM users is to give workloads that cannot use IAM roles the ability to make programmatic requests to AWS services using the API or CLI.
## IAM groups
- a collection of users
## IAM roles
- unlike users, roles are not an account you could sign in
- based on trust policy, it is dynamically applied to a set of accounts and services and defines what they can access
- roles provide temporary credentials and need to be renewed via AWS security token service. Temporary creds are always prefered over static long-term creds (e.g. I want to set up lambda to access Dynamo. If I give lambda a user, I risk leaking long-term creds. If I assign lambda roles, they only hold short-lived creds that are safer even if leaked)
# Principals
- they are human or workload that ath via IAM entity above. Roles get temporary creds, users get permanent creds.
## IAM policy
defines access permissions
- identity based policy
    - what resource can an identity access
- resource based policy
    - what principles can access the resource