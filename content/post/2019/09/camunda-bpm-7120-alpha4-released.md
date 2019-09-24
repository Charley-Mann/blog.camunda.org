+++
author = "Camunda BPM Team"
categories = ["Execution"]
tags = ["Release Note"]
date = "2019-08-26T10:00:00+01:00"
title = "Camunda BPM 7.12.0-alpha4 Released"
+++

**Camunda BPM 7.12.0-alpha4** is here and the highlights are:

* Handling Bpmn Error and Escalation in User Tasks
* Feature X
*
* [XX Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.12.0-alpha4)


You can [Download Camunda for free](https://camunda.com/download/) (click on Preview Release) or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).


If you are interested, you can see the complete [release notes](https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=15522).

If you want to dig in deeper, you can find the source code on [GitHub](https://github.com/camunda/camunda-bpm-platform/releases/tag/7.12.0-alpha4).

<!--more-->

## Handling Bpmn Error and Escalation in User Tasks

In case a business error occur during the exection of an External Task, it is possible to raise a Bpmn Error via the [Java API](https://docs.camunda.org/manual/latest/user-guide/process-engine/external-tasks/#reporting-bpmn-error) or [Rest API](https://docs.camunda.org/manual/latest/reference/rest/external-task/post-bpmn-error/).
But have you ever wanted to escalate an user task or indicate a busniss error for user task? Within this alpha you can now report a [Bpmn Error](https://docs.camunda.org/manual/latest/reference/bpmn20/events/error-events/#business-errors-vs-technical-errors) or an [Escalation](https://docs.camunda.org/manual/latest/reference/bpmn20/events/escalation-events/) for an user task.
Whenever a business error occur the user can invoke via [Java](https://docs.camunda.org/manual/latest/reference/bpmn20/tasks/user-task/#reporting-bpmn-error) or [Rest](https://docs.camunda.org/manual/latest/reference/rest/task/post-bpmn-error/) API a Bpmn Error which will be thrown:

```json
POST /task/aTaskId/bpmnError
{
  "errorCode": "process-order-error-543",
  "errorMessage": "An error occurred during processing order",
  "variables": {
	  "orderNumber" : {
		  "value" : "PL-233",
		  "type": "String"
	  }
  }
}
```
If in process is implemented an error catch event with the respective `errorCode`, the Bpmn Error will be catched and propagated accordingly. The same goes for the reporting an escalation, the respective documentation can be found as follows [Java](https://docs.camunda.org/manual/latest/reference/bpmn20/tasks/user-task/#reporting-bpmn-escalation) and [Rest](https://docs.camunda.org/manual/latest/reference/rest/task/post-bpmn-escalation/) API.

<!-- TODO consider adding an image depending on the rest of the features -->

## Cascading Changes to Due Dates of Recurring Timers
Within a BPMN model, a user can specify a recurring timer with an [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601#Repeating_intervals) interval string. (e.g. "R3/PT30M")
The timer stores a due date to indicate when it is ready for execution. For recurring timer, the engine calculates the due date based on the cycle (e.g. "PT30M") and creates a subsequent timer whenever a recurring timer is executed until the number of repeats ("R3" = repeated three times) is reached.

Given a "R3/PT30M" recurring, every timer instance is due 30 minutes after the previous one.
{{< figure src="no-change.png" alt="three 30-minute intervals indicating the three timer due dates">}}
Via the managementService it is possible to update the due date of a timer instance. If the due date of <b>Timer1</b> is altered by 15 minutes via `managementService.setJobDuedate(String jobId, Date newDuedate)` the three timers are executed differently.

{{< figure src="non-cascading-change.png" alt="timer 1 is due after 45min, timer 2 after 60min and timer 3 after 90min">}}
The due date of <b>Timer 1</b> was altered by +15 minutes, so it is due after 45 minutes. However, <b>Timer 2</b> and <b>Timer 3</b> are not affected by this and their due date is still based on the original due date of <b>Timer 1</b>.

However, what if you want a due date change to cascade to subsequent timer instances?
{{< figure src="cascading-change.png" alt="one 45-minute interval then two 30-minute intervals indicating the three timer due dates">}}
With this release you can use the `cascade` flag (via [REST API](https://docs.camunda.org/manual/latest/reference/rest/job/put-set-job-duedate/)) or `managementService.setJobDuedate(String jobId, Date newDuedate, boolean cascade)` to achieve this.

## Feature X

## What’s Next?
The next alpha version is scheduled for the end of October and our team is already working on it.

The minor release of Camunda BPM 7.12 is coming this fall (November 30, 2019).

## Your Feedback Matters!
With every release we constantly strive to improve Camunda BPM. To make this possible, we are reliant on your feedback. Feel free to share your ideas and suggestions with us.

You can contact us by writing a post in the forum.
