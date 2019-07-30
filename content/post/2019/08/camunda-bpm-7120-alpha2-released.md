+++
author = "Camunda BPM Team"
categories = ["Execution"]
tags = ["Release Note"]
date = "2019-08-02T0:00:00+01:00"
title = "Camunda BPM 7.12.0-alpha2 Released"
+++

**Camunda BPM 7.12.0-alpha2** is here and the highlights are:

* Error Message in BPMN End Event
* Add assigneeIn in Task Queries
* DMN 1.2 Schema support
* Clarified OpenJDK Support
* [XX Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.12.0-alpha2)

You can [Download Camunda for free](https://camunda.com/download/) (click on Preview Release) or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).


If you are interested, you can see the complete [release notes](LINK).

If you want to dig in deeper, you can find the source code on [GitHub](https://github.com/camunda/camunda-bpm-platform/releases/tag/7.12.0-alpha2).

<!--more-->



## Error Message in BPMN End Event


## Add assigneeIn in Task Queries


## DMN 1.2 Schema Support

As of this version, DMN models that use the DMN 1.2 namespace can be deployed into the process engine and parsed with the [DMN Model API](https://docs.camunda.org/manual/latest/user-guide/model-api/dmn-model-api/). Note that this is currently limited to the scope of implemented DMN 1.1 features. See our [DMN Implementation Reference](https://docs.camunda.org/manual/latest/reference/dmn11/) for details.

## Clarified OpenJDK Support

With the changes in Oracle's licensing model for OracleJDK, alternative JDKs and JDK builds have moved into the focus of our users. Camunda supports OpenJDK and IBM JDK already since version 7.0. Now, we have clarified that our OpenJDK support includes variants such Oracle OpenJDK, Adopt OpenJDK, Amazon Corretto and Azul Zulu. This applies to this alpha release, as well as previous releases until 7.9.0. See our [Supported Environments documentation](https://docs.camunda.org/manual/latest/introduction/supported-environments/#java-runtime) for a complete list.

## Engine Logging Configuration

We introduced two new configuration flags in the Process Engine Configuration that allows users to configure logging behavior.

<strong>Enable/Disable Logging Of Command Exceptions:</strong> Exceptions that occur during the execution of a command get caught and re-thrown to be handled by the calling user code. Whether or not these exceptions are logged can be configured by the new configuration flag enableCmdExceptionLogging. Bear in mind, that catching but not logging these exceptions can result in a loss of debugging/troubleshooting information. Also, when logging the exceptions, some exceptions might still be logged by the engine and thus can appear twice in the log (e.g., exceptions during job execution). By default, command exception logging is switched on.

<strong>Enable/Disable Reduced Job Exception Logging:</strong> The engine logs exceptions that occur during the execution of a job. Since a failed job can be retried and fail again, those exceptions might appear several times in the log. It is now possible to reduce the output by setting the enableReducedJobExceptionLogging configuration flag to true. This way, exceptions that occur while executing a job that has retries left are not logged. Only the last failure (that uses the last retry) are logged. A known limitation of this is when setting the job retries to 0 or 1 the job would execute once but an exception would not be logged since we currently have no way of checking left retries at the first execution.

## Stay Tuned!

This is the first alpha release on the road to **Camunda BPM 7.12** (due November 30, 2019). The next developer preview
(alpha 3) is scheduled in the end of August and will be packed with new features.

## Your Feedback Matters!

With every release, we strive to improve Camunda BPM. To make this possible, we are reliant on your feedback. Feel free to share your ideas and suggestions with us.

You can contact us by writing a post in the [forum](https://forum.camunda.org/).
