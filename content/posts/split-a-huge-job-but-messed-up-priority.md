---
title: 'I Starved a high priority job causing delayed transactions'
tags: [rca-stories, background-job, scheduling, tech-horror,  golang]
date:  "2024-02-03"
---


tldr; I split a long running job into smaller jobs but due to lack of proper prioritization of the jobs newly created numerous jobs starved the more important jobs.

#### The Big Background Job

We had a long running daily job. It would take 3-4 hours and we were fine with it since it was a background thing and we would run it at midnight. As the product grew and scaled we started to notice that it became longer and longer, 8-9 hours. Any machine restarts (autoscale events, cluster events,deployments) etc would cause the job to be not acked and the whole thing would run from the beginning again which was bad:
- duplicate work (although it was idempotent) 
- waste of time and resource
- actual job involved doing some api calls to external vendors and it was expensive


#### Splitting
So I split the big job into smaller ones. Job A would start and spawn 'n' smaller jobs enqueue them and exit. Job A would finish in minutes. Since smaller jobs by themselves finished quickly any restart was also not a major problem now.

But we started to notice that higher priority job (we had asynchronous flow for major transaction events) started getting starved. What happend was the smaller jobs inherited (programmatic error of copy paste) priority of the big job. (We quickly noticed this because we were monitoring queue length and also some business metric regarding transactions)


#### Solution
We deployed a fix with the right set of priorities for the job. (smaller background jobs got assigned a much lower priority)

We manually retried the bunch that were still in the queue.