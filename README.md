 # PagerDuty Solutions Consultant Challenge

PagerDuty (pronounced *pay+juh doo-tee*) is full-stack event intelligence and the end-to-end response orchestration platform. The PagerDuty service provides reliable incident notifications via email, push, SMS, and phone, as well as automatic escalations, on-call scheduling, and other functionality to help teams detect and fix infrastructure problems quickly.

Organizations taking advantage of the PagerDuty platform can visualize the health of services, infrastructure, and manage incident resolution all in one place. It provides actionable full-stack visibility and response orchestration to minimize cognitive load during alert investigation and identify the business impact of significant incidents.

<p align="center"><img width=55% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdlogo.png"></p>


## Main features

* The Pagerduty platform collects events from monitoring systems, triages what’s important, and involves the right people for a faster incident resolution response.
* Uses big-data visualizations to allow insight into the health of applications, services, and infrastructure while managing incident response workflows all in one place.
* Inspects the performance and efficiency from system level down to the user level. Pagerduty can be optimized to generate reports to measure operational efficiency, user productivity, and trends and patterns of recurring incidents.
* Includes built-in integrations with ITSM and ChatOps tools to recruit, collaborate, and share knowledge. Go beyond your on-call responders and mobilize experts to quickly address incidents in real time.
* PagerDuty is an always-on solution with guaranteed delivery by using globally distributed cloud, datacenter and service providers.




## Table of Contents

* [Setup](#setup)
  * [Trial Creation](#create-a-pagerduty-trial)
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

### Create a PagerDuty trial

You can create a trial account at PagerDuty.com with a 14 day license. You'll only need one hour to get going with this exercise.

<img width=15% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdsignup.png">

For this trial I've used the account of `bradwtesting.pagerduty.com`

If you are looking to fast forward your education of PagerDuty, you can optionally skip the configuration wizard and enable platform API access. To follow this exercise we will be using PagerDuty's API v2, with associated documentation that can be found [here](https://v2.developer.pagerduty.com).

<p align="left"><img width=20% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdapiaccess.png"></p>

PagerDuty API v2 access keys can be viewed and created under Configuration > API Access. I've created a v2 read/write key for the purposes of this exercise. Remember, API calls with PagerDuty are always going to be in `JSON` format with authorization tokens in the header to keep everything secure. Always have your token handy for using API requests.

<p align="left"><img width=40% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdexampleapi.png"></p>


 
 
 
 
 
 
## Account Setup

For the purposes of this workflow we will be creating a basic team, adding members, and associated schedule and escalation policies. Finally we will create a service and add any additional context information along the way.

### Creating a team via API

Creating a team with the PagerDuty API is pretty simple. You can copy paste the below text, replacing your `SECRET TOKEN` and will recieve a `201` response upon acceptance.

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


### Adding five users to team using API

Like the task before us, we can create five users using the API, then added them to the team. Among the team members I've manually varied their user id from 1 through 5, and altered their colors for quick identification in the PagerDuty UI. Sleepless nights belong to the red shirts on the Grognards team. This command optionally takes in a requester's email for log identification. Feel free to not include it!

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


For each user, we add them to the Grognards team. Matriculating through the `USERID` for each user. If this was done in a larger script one may think about handling `TEAMID` AND `USERID`s as variables.

```JSON
curl -X PUT --header 'Content-Type: application/json' --header 'Accept: application/vnd.pagerduty+json;version=2' --header 'Authorization: Token token=[SECRETTOKEN]' 'https://api.pagerduty.com/teams/[TEAMID]/users/[USERID]'
```
<p align="left"><img width=85% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddusersteammed.png"></p>


### Create a on-call schedule among five users through UI

Through the UI we will add everyone we want to the on-call schedule. Feel free to make this as simple or as complicated as you like. In my example I've let all the brad-Xs alternate one on call week off every other month. I'd hope they can appreciate it.

<img width=55% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdscheduling.png"><img width=45% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdsimpleschedule.png"></p>



### Create an Escalation Policy using API

To create an escalation policy you need a little information to get started. You need to retreieve the `USERID`, `TEAMID` and a `SERVICEID` if attaching to a service from the PagerDuty platform. You'll find that at the end of each url when browsing elements in the PagerDuty web UI, or you can pull them down as part of a larger script.

For this policy we are subjecting Brad-5, the newest member of the team to be the first at bat for handling problems. 


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
        "id": "[Grognards-team-ID]",
        "type": "team_reference"
      }
    ],
    "description": "Escalation for devops/brad-x team."
  }
}' 'https://api.pagerduty.com/escalation_policies'
```
<p align="left"><img width=65% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdescpolicy.png"></p>



### Setup contact methods for two user profiles through UI

For the purposes of this test I've set up a few profiles with SMS, email and iOS push notification capabilities. I suggest doing at least one during your trial of PagerDuty.

<p align="left"><img width=65% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdusercontact.png"></p>



### Create a Service using the API

As a majority of PagerDuty customers use an assortment of out of the box integrations [seen here]
(https://www.pagerduty.com/integrations/), we've elected to show how to create one from scratch, and we'll later use this same service to handle an incident.

For this service I've also gone ahead and added the escalation policies we've made before. 

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

For this portion of the demo we would like people to get in touch with how an incident is handled in real time. By creating an incident via API, you can test how team members will react and handle situations on the go. 

### Triggering an incident 

In this issue you'll notice we've opened an incident with "Lassy" falling down the well. We've attached it to the previously created service, and escalation policy outlined before.

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

From iOS we can see the incident and the actions we can take to response immediately.

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentack.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdincidentactions.png">
 

### Using Response Mobilizer 

Often members will want to mobilize teammates early to respond to an issue. This is two clicks within the PagerDuty mobile app.

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddresponsers.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdnotifyresponders.png">


### Using Subscriber Notification

We can also subscribe users regardless of team to the same issue.

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdopenack.png">


### Adding Notes

To decrease confusion members will add notes about the progress of an issue or updates taken to remediate the incident.

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdaddnote.png"><img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdnoteadded.png">



### Resolving via mobile app

At the end of the day the goal is quicker response time. If you think this isn't a pressing issue, you can 1 click resolve an incident and get on with your day.

<img width=35% src="https://github.com/bradweinstein/pagerduty-challenge/blob/master/images/pdacktimeline.png">

## Log Entries via API

Log Entries can be collected via API, for brevity of this document I reduced the response for which can be uploaded at request. I used the following command to collect the logs:

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

After completing this technical challenge I feel confident I could complete a PagerDuty demo, troubleshooting or installation session. 
If there are any questions please feel free to reach out via [email](mailto:bradleyseth.weinstein@gmail.com) or [LinkedIn](https://www.linkedin.com/in/bradleysweinstein/)


##### Helpful Links
* [PagerDuty API v2](https://v2.developer.pagerduty.com)
* [PagerDuty API Reference](https://v2.developer.pagerduty.com/v2/page/api-reference#!/API_Reference/get_api_reference)

