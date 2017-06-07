 # PagerDuty Solutions Consultant Challenge

PagerDuty (pronounced *pay+juh doo-tee*) is full-stack event intelligence and the end-to-end response orchestration platform. The PagerDuty service provides reliable incident notifications via email, push, SMS, and phone, as well as automatic escalations, on-call scheduling, and other functionality to help teams detect and fix infrastructure problems quickly.

Organizations taking advantage of the PagerDuty platform can visualize the health of services, infrastructure, and manage incident resolution all in one place. It provides actionable full-stack visibility and response orchestration to minimize cognitive load during alert investigation and identify the business impact of significant incidents.

<p align="center"><img width=55% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdlogo.png"></p>


## Main features

* Platform collects events from monitoring systems, triages what’s important, and involves the right people for fast incident resolution
* Visualizes the health of applications, services, and infrastructure while managing incident response workflows all in one place.
* Built-in integrations with ITSM and ChatOps tools to recruit, collaborate, and share knowledge. Go beyond your on-call responders and mobilize to address incidents in real time.
* Visualize and analyze performance and efficiency from the system level down to the user level.  View reports to measure operational efficiency, user productivity, and trends and patterns of recurring incidents.
* An always-on solution with guaranteed delivery by using globally distributed cloud, datacenter and service providers.




## Table of Contents

* [Setup](#setup)
  * [Trial Creation](#create-pagerduty-trial)
* [Account Setup](#account-setup)
  * [Creating a team via API](#creating-a-team-via-api)
  * [Adding Users](#adding-5-users-to-team-using-api)
  * [Creating an on-call schedule](#create-a-on-call-schedule-among-5-users-through-ui)
  * [Creating an escalation policy via API](#create-an-escalation-policy-using-api)
  * [Creating user profiles](#setup-contact-methods-for-2-user-profiles-through-ui)
  * [Creating user profiles](#create-a-service-using-the-api)
* [Incident Response Workflow](#incident-response-workflow)
  * [Triggering an incident via PagerDuty API](#triggering-an-incident)
  * [Acknowleding Incidents via PagerDuty mobile](#acknowledge-the-incident)
  * [Using Response Mobilizer](#using-response-mobilizer)
  * [Using Subscribers Notification](#using-subscriber-notification)
  * [Adding notes](#adding-notes)
  * [Resolving via PagerDuty mobile](#resolving-via-mobile-app)
* [Log Entries](#log-entries-via-api)
* [Conclusion](#conclusion)
* [Helpful Links](#helpful-links)
 


## Setup

### Create PagerDuty trial

Trial account created under `bradwtesting.pagerduty.com` at PagerDuty.com

<img width=15% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdsignup.png">

Skipped through configuration wizard and enabled platform API access. I will be using PagerDuty's API v2, and associated documentation can be found [here](https://v2.developer.pagerduty.com).

<p align="left"><img width=20% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdapiaccess.png"></p>

API v2 access keys can be viewed and created under Configuration > API Access. I've created a v2 read/write key for the purposes of this exercise.

<p align="left"><img width=40% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdexampleapi.png"></p>


 
 
 
 
 
 
## Account Setup

### Creating a team via API

```JSON
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'Authorization: Token token=[SECRETTOKEN]' -d '{
  "team": {
    "type": "team",
    "name": "Grognards",
    "description": "Devops Team"
  }
}' 'https://api.pagerduty.com/teams'
```
<p align="left"><img width=85% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdteamsapi.png"></p>


### Adding 5 users to team using API

Created 5 users using the API, then added them to the team.
```JSON
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'From: [PRIVATE EMAIL]' --header 'Authorization: Token token=[SECRETTOKEN]' -d '{
  "user": {
    "type": "user",
    "name": "brad-[1-5]",
    "email": "[PRIVATE EMAIL]",
    "time_zone": "America/New_York",
    "color": "green",
    "role": "admin",
    "job_title": "DevOps Wizard",
    "description": "User[1-5]"
  }
}' 'https://api.pagerduty.com/users'
```
<p align="left"><img width=85% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddusersnoteam.png"></p>


For each user, they were added to the Grognards team.

```JSON
curl -X PUT --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'Authorization: Token token=[SECRETTOKEN]' 'https://api.pagerduty.com/teams/[TEAMID]/users/[USERID]'
```
<p align="left"><img width=85% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddusersteammed.png"></p>


### Create a on-call schedule among 5 users through UI

### Create an Escalation Policy using API

### Setup contact methods for 2 user profiles through UI

### Create a Service using the API





## Incident Response WorkFlow

### Triggering an incident 

### Acknowledge the Incident

### Using Response Mobilizer 

### Using Subscriber Notification

### Adding Notes

### Resolving via mobile app


## Log Entries via API



## Conclusion

After completing this technical challenge I feel confident I could easily complete a PagerDuty troubleshooting or installation session. I'd like to give my thanks to the Datadog crew for letting me attempt this challenge.

If there are any questions please feel free to reach out via [email](mailto:bradleyseth.weinstein@gmail.com) or [LinkedIn](https://www.linkedin.com/in/bradleysweinstein/)


##### Helpful Links
* [PagerDuty API v2](https://v2.developer.pagerduty.com)
