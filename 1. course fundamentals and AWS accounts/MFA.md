## why do we need MFA?
- if username and password are leaked, anyone can be you
- factors are used to prove evidence
- factors: knowledge (username, password) , possession (something you have, like an MFA device),
inherent (fingerprint, face scan, voice scan), location(physical or network location)
- the more factors, more the security.

## how does AWS handle a second factor for log in?

- by default, we use knowledge (username and password). This is single factor auth
- to prevent any problems caused by leak, we use MFA
- we use a physical or virtual MFA device like google authenticator


