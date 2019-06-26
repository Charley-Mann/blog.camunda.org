+++
author = "Camunda BPM Team"
categories = ["Execution"]
tags = ["Release Note"]
date = "2019-06-25T11:00:00+01:00"
title = "Camunda BPM 7.12.0-alpha1 Released"
+++

**Camunda BPM 7.12.0-alpha1** is here and the highlights are:

* Feature 1 ...
* Feature 2 ...
* Feature 3 ...
* [?? Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.12.0-alpha1)

You can [Download Camunda for free](https://camunda.com/download/) (click on Preview Release) or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).


<!-- todo: adjust me --> If you are interested, you can see the complete [release notes](https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=15390).

If you want to dig in deeper, you can find the source code on [GitHub](https://github.com/camunda/camunda-bpm-platform/releases/tag/7.12.0-alpha1).

<!--more-->

## Cockpit: BPMN Documentation
With this alpha Release, we introduce another functionality to make managing Processes in Cockpit easier: Viewing the documentation of your processes. If you documented all or part of your process, you can now view it in the Process Definition as well as the Process Instance view. This way, you don't need to switch to the modeler when you want to access the documentation.

Documented Elements are marked with an <button class="btn btn-xs"><i class="glyphicon glyphicon-info-sign"></i></button> on the BPMN Diagramm. Clicking it will open a dialog to view the documentation.

All Documented Elements are also listed in the table below. Clicking on a row will highlight the corresponding element in the Diagram.

{{< figure src="bpmn_documentation.png" alt="Process Documentation">}}

> Please bear in mind that this feature is only available in the Enterprise Edition of the Camunda BPM platform. To try it out anyway, please request a [Free Trial or Quote](https://camunda.com/enterprise/)

## Cockpit: Embedded Stacktraces
Not everything works according to plan. Camunda provides a lot of tools to debug your process, including stacktraces from failed jobs. Previously, these were only viewable in a separate window. This window does not contain information about the process itself and is not as helpful to an administrator or support team.

We now display incident stacktraces directly in cockpit, making it unnecessary to open new tabs and generating new links. Of course, you can still directly link to the embedded stacktrace in case you need to send it to someone. Just copy the Browser URL with the stacktrace open.

{{< figure src="variable_filters.png" alt="Variable Filter View">}}

## Feature 3

<!--no-more-->

## What's Next?

This is the fourth alpha release on the road to **Camunda BPM 7.11** (due May 31, 2019). Please have a look at our [roadmap](https://camunda.com/learn/community/#roadmap) for what's still to come.

## Your Feedback Matters!

With every release, we strive to improve Camunda BPM. To make this possible, we are reliant on your feedback. Feel free to share your ideas and suggestions with us.

You can contact us by writing a post in the [forum](https://forum.camunda.org/).