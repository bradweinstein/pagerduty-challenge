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
  * [Adding Users via API](#adding-5-users-to-team-using-api)
  * [Creating an on-call schedule via UI](#create-a-on-call-schedule-among-5-users-through-ui)
  * [Creating an escalation policy via API](#create-an-escalation-policy-using-api)
  * [Creating user profiles via UI](#setup-contact-methods-for-2-user-profiles-through-ui)
  * [Creating a service via API](#create-a-service-using-the-api)
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

<img width=50% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdscheduling.png"><img width=50% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdsimpleschedule.png"></p>



### Create an Escalation Policy using API

```JSON
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'From: [PRIVATE EMAIL]' --header 'Authorization: Token token=[SECRETTOKEN]' -d '{
  "escalation_policy": {
    "type": "escalation_policy",
    "name": "DevOps Team Escalation Policy",
    "escalation_rules": [
      {
        "escalation_delay_in_minutes": 30,
        "targets": [
          {
            "id": "[UID OF BRAD-5]",
            "type": "user_reference"
          }
        ]
      }
    ],
    "repeat_enabled": true,
    "services": [
      {
        "id": "[SERVICE-ID]",
        "type": "service_reference"
      }
    ],
    "num_loops": 2,
    "teams": [
      {
        "id": "[Grognards-team-ID",
        "type": "team_reference"
      }
    ],
    "description": "Escalation for devops/brad-x team."
  }
}' 'https://api.pagerduty.com/escalation_policies'
```
<p align="left"><img width=65% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdescpolicy.png"></p>



### Setup contact methods for 2 user profiles through UI

Setup repeated for Brad-2

<p align="left"><img width=65% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdusercontact.png"></p>



### Create a Service using the API

```JSON
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'Authorization: Token token=[SECRETTOKEN]' -d '{
  "service": {
    "type": "service",
    "name": "My Neato Hacking Website",
    "description": "Services we wish upon our enemies.",
    "auto_resolve_timeout": 14400,
    "acknowledgement_timeout": 600,
    "status": "active",
    "escalation_policy": {
      "id": "[DEVOPS ESC POLICY ID]",
      "type": "escalation_policy_reference"
    },
    "incident_urgency_rule": {
      "type": "use_support_hours",
      "during_support_hours": {
        "type": "constant",
        "urgency": "high"
      },
      "outside_support_hours": {
        "type": "constant",
        "urgency": "low"
      }
    },
    "support_hours": {
      "type": "fixed_time_per_day",
      "time_zone": "America/New_York",
      "start_time": "09:00:00",
      "end_time": "17:00:00",
      "days_of_week": [
        1,
        2,
        3,
        4,
        5
      ]
    },
    "scheduled_actions": [
      {
        "type": "urgency_change",
        "at": {
          "type": "named_time",
          "name": "support_hours_start"
        },
        "to_urgency": "high"
      }
    ]
  }
}' 'https://api.pagerduty.com/services'
```
<p align="left"><img width=65% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdserviceapi.png"></p>



## Incident Response WorkFlow

### Triggering an incident 

```JSON
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'From: [PRIVATE EMAIL]' --header 'Authorization: Token token=[SECRETTOKEN]' -d '{
  "incident": {
    "type": "incident",
    "title": "Lassy fell down the well!!",
    "service": {
      "id": "[SERVICE-ID]",
      "type": "service_reference"
    },
    "incident_key": "baf7cf21b1da41b4b0221008339ff357",
    "body": {
      "type": "incident_body",
      "details": "Lassy has fallen down the well in this region. Please read http://www.imdb.com/title/tt0110305/ for further information. If unable to help Lassy out of the well, Lassy could safe itself within 5 minutes."
    },
    "escalation_policy": {
      "id": "[DEVOPS ESC POLICY]",
      "type": "escalation_policy_reference"
    }
  }
}' 'https://api.pagerduty.com/incidents'
```
<p align="left"><img width=85% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentcreated.png"></p>



### Acknowledge the Incident

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentack.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentactions.png">
 

### Using Response Mobilizer 

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddresponsers.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdnotifyresponders.png">



### Using Subscriber Notification
<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentack.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdopenack.png">


### Adding Notes

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddnote.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdnoteadded.png">



### Resolving via mobile app

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdacktimeline.png">

## Log Entries via API

Log Entries can be collected via API, for brevifty of this document I can include the response for further documentation. I used the following command to collect the logs:

```bash
curl -X GET --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'Authorization: Token token=[SECRETTOKEN]' 'https://api.pagerduty.com/incidents/[LASSY-INCIDENT-ID]/log_entries?time_zone=UTC&is_overview=false&include%5B%5D=incidents&include%5B%5D=services&include%5B%5D=channels&include%5B%5D=teams'
```

Response:
```JSON
{
  "log_entries": [
    {
      "id": "[INCIDENTID]",
      "type": "resolve_log_entry",
      "summary": "Resolved by brad-1",
      "self": "https://api.pagerduty.com/log_entries/[INCIDENTID]",
      "html_url": null,
      "created_at": "2017-06-08T01:24:05Z",
      "agent": {
        "id": "[USERID]",
        "type": "user_reference",
        "summary": "brad-1",
        "self": "https://api.pagerduty.com/users/[USERID]",
        "html_url": "https://bradwtesting.pagerduty.com/users/[USERID]"
      },
      "channel": {
        "type": "mobile"
      },
      "service": {
        "id": "[SERVICEID]",
        "name": "My Neato Hacking Website",
        "description": "Services we wish upon our enemies.",
        "auto_resolve_timeout": 14400,
        "acknowledgement_timeout": 600,
        "created_at": "2017-06-08T00:48:01Z",
        "status": "active",
        "last_incident_timestamp": "2017-06-08T01:13:27Z",
        "teams": [
          {
            "id": "[TEAMID]",
            "name": "Grognards",
            "description": "Devops Team",
            "type": "team",
            "summary": "Grognards",
            "self": "https://api.pagerduty.com/teams/[TEAMID]",
            "html_url": "https://bradwtesting.pagerduty.com/teams/[TEAMID]",
            "default_role": "manager",
            "privilege": null
          }
        ],
        
     ...
     
```


## Conclusion

After completing this technical challenge I feel confident I could complete a PagerDuty demo, troubleshooting or installation session. I'd like to give my thanks to the PagerDuty crew for letting me attempt this challenge.

If there are any questions please feel free to reach out via [email](mailto:bradleyseth.weinstein@gmail.com) or [LinkedIn](https://www.linkedin.com/in/bradleysweinstein/)


##### Helpful Links
* [PagerDuty API v2](https://v2.developer.pagerduty.com)
* [PagerDuty API Reference](https://v2.developer.pagerduty.com/v2/page/api-reference#!/API_Reference/get_api_reference)

