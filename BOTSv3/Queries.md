# Introduction

This file saves as documentation of my progress in CTF BOTSv3 by Splunk. (Comment: questions will probably not be in order)

Question 200:
 - List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

Query used:
```bash
index=botsv3 sourcetype="aws:cloudtrail"
| top limit=20 "userIdentity.userName"
```
Answer:

splunk_access,web_admin,bstoll,btun
