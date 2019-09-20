What is going on in my process engine?

# The problem

TODO: this post looks at delayed/"stuck" job execution

# Who takes care of executing processes?

Process execution in Camunda BPM is done by two types of threads:

1. Application threads
2. Job Executor threads

Application threads are threads that call the Camunda Java API from an application context. For example, if we have a REST endpoint that starts a process instance, the HTTP worker thread will invoke `RuntimeService#startProcessInstanceByKey` and run the process instance until wait states are reached. The calling thread receives immediate feedback: If the call fails, we can catch and and handle an exception. If the call does not return, we can debug directly into our process's delegate code and see where execution gets stuck.

The second type of threads are job execution threads. A job in the process engine is a piece of work that needs to be done at *some* time in the future. In the case of BPMN timer events, this point in time is defined in the BPMN model. In the case of asynchronous continuations, the time is as soon as possible. Job execution threads take care of this work and are managed by a component called the *job executor*. Being more detached from the application, it is often harder to understand when job execution happens or more importantly when it does not happen or when it is slow.

# The Job Executor

On the highest level, we can think of the job executor as a thread pool that picks and executes jobs whenever they become available. One level deeper, we have the following components:

<Bild von Job-Executor-Komponenten>

https://docs.google.com/drawings/d/1RFVTSXeLbC0Yqzz4ln1ZnlFma7uewiAFyHGapydYkIY/edit?usp=sharing

1. The table `ACT_RU_JOB` in the Camunda database schema
2. A thread called *job acquisition*
3. A thread pool consisting of a work queue and collection of threads

From creation to completion, a job makes its way through each of these components:

<Bild von Komponenten mit Lifecycle>

1. The job is inserted into `ACT_RU_JOB`, e.g. when a process instance reaches an asynchronous continuation
2. Job acquisition picks up the job by polling `ACT_RU_JOB`. To ensure a job is only acquired once, a time-based lock is set in the database.
3. Job acquisition submits the job into the thread pool's queue.
4. As soon as a job execution thread is free, it picks up the job from the queue and executes its business logic. Once done, it deletes the job from `ACT_RU_JOB`

# What can go wrong and how do I find the cause?

Generally jobs can get stuck or delayed in any stage of this pipeline. In the following we will look at common causes and the tools to diagnose each component.

## Diagnosing `ACT_RU_JOB`

Problem:

* Jobs are never or only very late picked up from the database (TODO: rephrase)

Identifying the problem:

* Inspect the table `ACT_RU_JOB`. If a job's `LOCK_EXP_TIME_` and `LOCK_OWNER_` fields are empty, it has has not been picked up by job acquisition.

Possible causes and solutions:

* The job is not yet available for execution.
  * Check the `DUE_DATE_` column to see if the job is scheduled for a future date (e.g. in case of a BPMN timer).
  * Check the `SUSPENDED_` column to see if a job is suspended, in which case it is ignored by job acquisition (`1` = job is active; `2` = job is suspended). Use the `ManagementService` API, REST API or Cockpit to activate the job or process instance.
* Job execution is overloaded. If you produce jobs at a higher rate than the job executor can consume them, then . Consider adding more processing resources (e.g. growing the job execution thread pool or setting up a second job executor).

## Diagnosing Job Acquisition

Problem: A job is available, but still not locked and picked up for execution or only at a low rate.

Identifying the problem:

* Set the logger `` to `DEBUG`. This will produce output as follows:

```

```

Job acquisition is no longer a black box but we can clearly see how it works: When is `ACT_RU_JOB` polled, which SQL query is used, and which jobs are found and then submitted for execution.

Possible causes and solutions:

* The job acquisition query performs not well enough. For example, if we can query for one job in one second, our throughput is limited to 60 jobs per minute.
  * Improve query performance. Start with generating query plans and then analyze where time is spent.
  * Increase the number of jobs acquired with every query. Often, the performance of the query does not depend of the number of jobs selected. Set the process engine configuration property `maxJobsToAcquire` (TODO: check and link) to a higher value.
* The acquisition query's predicate `DEPLOYMENT_ID_ IN (...)` does not include the job's deployment id
  * If you do not use a shared process engine with process applications (e.g. you deploy processes only via REST API): Deactivate the process engine configuration property `jobExecutorDeploymentAware` (TODO: check and link).
  * Else: Provide the process as part of a process application. During deployment, the deployment will be registered with the job executor. Alternatively, register the deployment manually with the job executor via `ManagementService#registerDeploymentForJobExecutor`.
* Acquisition polls the table not often enough (TODO: better wording)
  * If the job table is empty, job acquisition will apply exponential backoff to its polling. Use the process engine configuration properties `fooWait` and `barWait` (TODO: check and link) to control the backing off.
  * Set up a second job executor to have two acquisition threads.
* Jobs are no longer picked up, because the thread pool's queue is full and remains in that sate. This indicates that job execution threads are blocked (e.g. waiting for a response they never receive).
  * Take a JVM thread dump to verify this cause and find the blocking line of code. If the threads are blocked in your delegation code, try to rewrite the code such that unlimited blocking can never occur.

## Diagnosing the thread pool

Problem: Too much time elapses between acquisition and execution of a job.

Identitying the problem:

As above, set the logger `` to `DEBUG`. This will produce output as follows:

```
```

Compare the timestamps of the log entries to see how much time passes between acquisition and execution.

Possible causes and solutions:

* Execution threads are busy with other jobs
  * Increase the number of threads in the thread pool. Note: More threads can only help if the CPU is not already running at maximum capacity. Measure if this does actually improve throughput.
  * Decrease the time a job takes to run. For example, if a job makes an expensive computation, try to optimize your code or use [external service tasks]() instead, so that the computation is not performed by the job execution thread pool.
* The queue has grown too large
  * Decrease the size of the queue. Job acquisition will not acquire as many jobs anymore, but will wait for the queue to drain.

## Diagnosing a job during execution

Problem: Job execution begins, but it never completes or takes a long time to complete.

Identifying the problem:

* If the job is stuck, take a JVM thread dump to understand where it is stuck.
* If the job is slow, activate debug logging for involved components to understand where time is spent. Set the logger `` to `DEBUG` to see all queries the process makes and how much time they take.

## Working with Log Files

Some general hints when working with log files:

* Always include thread IDs in the log output. When reading the files, make sure to not mix up log output from different threads.
* Learn how to configure logging in the environment in which you use Camunda. (TODO: links). Some resources for common setups:
  * [Camunda documentation on logging]()
  * [Configuring logging in Spring Boot projects]()
  * [Configuring logging on Tomcat](). Don't forget that Tomcat uses JULI, where log levels have different names. `DEBUG` is `FINE`.
  * [Configuring logging on Wildfly]()

## Happy Troubleshooting

If the job executor was a black box to you, I hope this blog post has raised the curtain and enables you to help yourself when you encounter problems. In any case, we are happy to help you on the [Camunda user forum](). Share with us what you have tried.