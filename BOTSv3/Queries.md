# Introduction

This file saves as documentation of my progress in CTF BOTSv3 by Splunk. (Comment: questions will probably not be in order)

# QQA (Questions,Queries,Answers)

**Question 200:**
 - List out the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly's AWS environment? Answer guidance: Comma separated without spaces, in alphabetical order. (Example: ajackson,mjones,tmiller)

**Query used:**
```bash
index=botsv3 sourcetype="aws:cloudtrail"
| top limit=20 "userIdentity.userName"
```
**Answer:**

splunk_access,web_admin,bstoll,btun

<hr>

**Question 201:**
- What field would you use to alert that AWS API activity have occurred without MFA (multi-factor authentication)? Answer guidance: Provide the full JSON path. (Example: iceCream.flavors.traditional)

**Query used:**
```bash
index=botsv3 eventType="AwsApiCall"
| spath userAgent
| search userAgent="signin.amazonaws.com"
| spath "userIdentity.sessionContext.attributes.mfaAuthenticated"
| search "userIdentity.sessionContext.attributes.mfaAuthenticated"=false
```

**Answer:**

userIdentity.sessionContext.attributes.mfaAuthenticated

**Personal commentary:**
 
- I think I could've just seen this within filters and didn't have to go through all the additional filtering as I first filtered only AwsApiCall and then searched from there within the logs.
<hr>
