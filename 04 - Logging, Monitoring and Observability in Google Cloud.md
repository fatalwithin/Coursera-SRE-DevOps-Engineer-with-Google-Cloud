
- [Overview](#overview)
  - [Monitoring Tools](#monitoring-tools)
  - [Operations-based Tools](#operations-based-tools)
  - [Application Performance Management Tools](#application-performance-management-tools)
  - [Why Monitor?](#why-monitor)
  - [Critical Measures](#critical-measures)
  - [Four Golden Signals](#four-golden-signals)
    - [SLIs, SLOs, SLAs](#slis-slos-slas)
    - [Choosing a good SLI](#choosing-a-good-sli)
    - [Specifying SLIs](#specifying-slis)
    - [Developing SLOs and SLIs](#developing-slos-and-slis)
  - [Developing an Alerting Strategy](#developing-an-alerting-strategy)
  - [Creating Alerts](#creating-alerts)
  - [Creating Alerting Policies with the CLI](#creating-alerting-policies-with-the-cli)
  - [Service Monitoring](#service-monitoring)
- [Monitoring Critical Systems](#monitoring-critical-systems)
  - [Observability Architecture](#observability-architecture)
  - [Dashboards](#dashboards)
    - [Understanding Dashboards](#understanding-dashboards)
    - [Creating Charts](#creating-charts)
    - [Dashboard Construction](#dashboard-construction)
  - [Uptime Checks](#uptime-checks)
  - [Working with Agents](#working-with-agents)
    - [Monitoring](#monitoring)
    - [Logging](#logging)
    - [Baking an Image](#baking-an-image)
  - [Non-VM Resources](#non-vm-resources)
  - [Exposing Custom Metrics](#exposing-custom-metrics)
  - [Strategic Logging](#strategic-logging)
    - [Labeling](#labeling)
    - [Working with the Log Viewer](#working-with-the-log-viewer)
    - [Using Log-Based Metrics](#using-log-based-metrics)
  - [Exporting and Analyzing Logs](#exporting-and-analyzing-logs)
  - [Error Reporting](#error-reporting)
- [Monitoring Network Security and Audit Logs](#monitoring-network-security-and-audit-logs)
  - [Monitoring Network Security](#monitoring-network-security)
    - [VPC Flow Logs](#vpc-flow-logs)
    - [Firewall Rules Logging](#firewall-rules-logging)
    - [Cloud NAT Logs](#cloud-nat-logs)
    - [Packet Mirroring](#packet-mirroring)
    - [Network Intelligence Center](#network-intelligence-center)
  - [Monitoring Audit Logs](#monitoring-audit-logs)
    - [Audit Logs](#audit-logs)
    - [Data Access Logging](#data-access-logging)
    - [Understanding Audit Logs](#understanding-audit-logs)
    - [Best Practices for Audit Logs](#best-practices-for-audit-logs)
  - [Incident Management](#incident-management)
  - [Declaring an Incident](#declaring-an-incident)
  - [Restoring Service](#restoring-service)
  - [Preventing Recurrence](#preventing-recurrence)
  - [Debugger](#debugger)
  - [Trace](#trace)
  - [Profiler](#profiler)
  - [The Costs of Monitoring](#the-costs-of-monitoring)
  - [Bill Estimation](#bill-estimation)
  - [Cost Control Best Practices](#cost-control-best-practices)
# Overview

Welcome to Introduction to Google Cloud Monitoring Tools. In this module, we'll take some time to do a high-level overview of the various products that constitute Google Cloud's logging, monitoring and observability suite. The core operations tools in Google Cloud breakdown into two major categories. The operation's focus components including logging, monitoring, error reporting and service monitoring, tend to be more for personnel who are primarily interested in infrastructure and keeping that infrastructure up, running and error-free. The application performance management tools including debugger, trace and profiler in contrast, tend to be more for developers who are trying to perfect or troubleshoot applications that are running in one of the Google Cloud compute products. But it isn't fair to think of these tools as belonging purely to either one of these two groups. A developer would, of course, sometimes need access to logs or monitoring metrics, just like an operations team member might need to trace latency. For reference the homepage for documentation related to this course can be found at http://cloud.google.com/stackdriver/docs

## Monitoring Tools

Let's start with an overview of why we need these tools, and then we'll spend a little time getting to know both operations and the application performance management products.  

If you've ever worked with on-premise environments, you know that you or someone in your organization can actually physically touch any of your servers. If an application becomes unresponsive, someone can walk in and physically investigate. In the Cloud though, the servers aren't yours, they're Google's, and you won't be able to inspect them physically. The question becomes, how do you know what's happening with your server, or database, or application? The answer is the tools discussed in this course. It all starts with the signals. Metrics, logging and trace data capturing is integrated into Google products from the hardware layer up. From those products, the signal data flows into Google Cloud's operations tools, where you can visualize it in dashboards and through the Metrics Explorer. You can dissect and analyze automated and custom logs in the Log Viewer, monitor services for compliance with service-level objectives, SLOs and track error budgets, use health checks to check up time and latency for external facing sites and services. You can also debug and profile running applications. When indicators pickup possible problems, signal data can generate notifications to code or through various information channels to key personnel. Error reporting can help operations and developer teams spot, count, and analyze crashes in Cloud-based services. The visualization and analysis tools can then help troubleshoot what's exactly happening in Google Cloud. Ultimately, you won't miss that easy server access, because Google's going to allow you more precise insights into your Cloud installed than you ever had on-premise.  

Google Cloud has many products from Kubernetes, to BigQuery, to Spanner, and they all stream metrics and logs into Google Cloud's logging and Cloud monitoring components. The logs router determines where the data goes and can be used to exclude some type of cell entries, or to route logs to external locations like Pub/Sub or BigQuery, perhaps for automated handling and for long-term storage and analysis. An auditor might inspect a Logs Viewer to see when a given Spanner instance was created and by whom. Security personnel could use threat detection or the Security Command Center to spot and analyze intrusion attempts. A network engineer might run SQL queries and BigQuery to better understand network flow. In addition to role monitoring metrics and logging entries, Google Cloud also helps SysOps, SRE, and DevOps personnel analyze and improve application performance. Take as an example, an HTTP based Java service running inside of a Compute Engine VM. Debugger would allow the inspection of the services code state without stopping or degrading its performance. It helps answer the question, what was happening in the code when this particular line executed? Similarly, Profiler can also be used to examine CPU and memory use to help spot bottlenecks and improve algorithmic performance. Trace is all about analyzing latency in a multi-layer microservice application. The Logs API can be used by developers to write directly to Google Cloud logs.  

## Operations-based Tools

Now that we've introduced the products in Google Clouds logging and monitoring tool suite, let's take a closer look. Let's start with the products that tend to be of interest for the operations folk **monitoring, logging, error reporting and service monitoring**.

When DevOps personnel think about tracking exactly what's happening inside Google Cloud projects, **monitoring** is often the first consideration. It's mentioned first on the documentation homepage, just like the first product in the Operations Section of the Google Cloud Navigation menu. As we stated previously, **monitoring starts with signal data**. When the data scientists are running massively scalable queries in BigQuery, knowing how many queries are currently in flight, how many bytes have been scanned and added to the bill, and data slot usage patterns all will be important.

It could also be critical to DevOps teams running containerized applications in Cloud Run to know CPU and memory **utilization** and have built on.

Workloads on Compute Engine will benefit from CPU and memory utilization data along with **Uptime, disk throughput** and scores of others.

And if those same DevOps teams want to augment the signal metrics coming out of their custom application wherever it's running, they could use the open source **Open Telemetry and create their own metrics**.

Google Cloud by default collects more than 1000 different streams of metric data, which can be incorporated into **dashboards**, **alerts** and a number of other key tools.

Here we see a project running a Compute Engine VM instance with the logging and monitoring agents installed. Cloud monitoring provides visibility into the performance uptime and overall health of cloud powered applications. It collects metrics, events and metadata from projects, logs, services, systems, agents, custom code and various common application components. Including Cassandra, Nginx, Apache web server and Elastic Search.

Monitoring ingest that data and generates insights via **dashboards, metrics, explorer charts and automated alerts**.

Google Cloud logging is all about collecting, storing, searching, analyzing, monitoring and alerting on log entering events.

Automated logging is integrated into Google Cloud products like App Engine, Cloud Run, Compute Engine VMs that run the logging agent and GKE.

Export log data as files to cloud storage as messages through Pub/Sub or into BigQuery tables. Logs based metrics may be created and integrated into Cloud monitoring dashboards, alerts and service SLOs. Pub/Sub messages can be analyzed in near real time using custom code or stream processing technologies like data flow. **BigQuery** allows analysts to examine logging data through **SQL queries**, and archive log files in cloud storage can be analyzed with several tools and techniques.

Default log retention and cloud logging depends on the log time. Data Access logs are retained **by default for 30 days.** But this is configurable up to a **maximum of 3,650 days**. **Admin logs are stored by default for 400 days**, you can export logs to Cloud Storage or BigQuery to extend retention periods. The Google Cloud Platform logs that are are visible to you in Cloud login vary depending on which Google Cloud resources you're using in your Google Cloud project organization.

**Three key log categories** are 

- audit logs, 
- agent logs, 
- network logs.

**Cloud Audit Logs** help answer the question. **Who did what, where and when?** 

- **Admin Activity** tracks configuration changes. 
- **Data Access** tracks, calls that read configuration or metadata resources, as well as user driven calls that create, modify or read user provided resource data.
- **System Events** are non human Google Cloud administrative actions that change the configuration of resources. 
- **Access transparency** provide you with logs that capture the actions Google personnel take when accessing your content.

**Agent Logs** use a Google customized package **fluentd** agent that can be installed on any AWS or Google Cloud VM to ingest log data from Google Cloud instances. For example, Compute Engine or manage VMs, or containers as well as AWS EC2 instances.

**Network Logs** provide both network and security operations within depth and network service telemetry. 

- VPC flow logs record samples of VPC network flow and can be used for network monitoring forensics, real time security analysis and expense optimization. 
- Firewall rules logging allows you to audit, verify and analyze the effects of your firewall rules. 
- NAT gateway logs capture information on Nat network connection and errors. 

**Error reporting** counts, analyzes and aggregates the crashes in your running cloud services. Its management interface displays the results with **sorting and filtering capabilities**. A dedicated view shows the error details, time chart, occurrences, affected user account, first and last seen dates and cleaned exception stack trace. You can also create alerts receive notifications on new errors.

**Service monitoring** helps organisations understand and troubleshoot intra-service dependencies. With current support for **App Engine, Anthos Service Mesh and Istio.** Service monitoring streamlines the creation of latency and error based SLOs to make it easy for organizations to spot customer pain.

When SLOs are created, it also tracks and reports error budget burn, to make it clear to DevOps team members when there's budget to spend, and when things should be left alone.

## Application Performance Management Tools

Now that we've explored the operations based tools, let's spend a little time on the tools designed to help with application performance management. Namely **debugger**, **tracer** and **profiler**.

Google clouds debugger lets you debug your applications while they're running in production. Without stopping them or slowing them down so you can examine your codes function and performance under actual production conditions.

Easily collaborate with other team members by **sharing your debug session**. Sharing a debug session is as easy as sending the console URL.

Capture the state of your application in production at a specific line location with snapshots. Use **log points** to inject new logging statement on demand at a specific line location. Capture a snapshot or write a log point message only when you need it using a simple conditional expression, written in your applications language. Cloud Debugger is easily integrated into existing developer workflows, launch debugger, and take snapshots directly from Cloud logging, Error Reporting dashboards, IDs, and the GCloud command line interface.

And debugger knows how to display the correct version of the source code. Because it easily integrates with a version control systems such as cloud source repositories, **GitHub, Bitbucket, or GitLab**.

Based on the tools Google uses on its production services, cloud traces a **tracing system that collects latency data from your distributed applications** and displays it in the Google Cloud Console. Trace can capture traces from applications deployed on App Engine, Compute Engine VMs, and Google Kubernetes Engine containers.

Performance insights are provided in near-real-time. And Trace automatically analyzes all of your applications traces to generate in depth latency reports that can service performance segregations.

Trace continuously gathers and analyzes trace data to automatically identify recent changes to your applications performance. Poorly performing code increases the latency and cost of applications and web services every day without anyone knowing or doing anything about it. 

Cloud profiler changes this by using statistical techniques and extremely low impact instrumentation. That could run across all production application instances, to **provide a complete CPU and heat picture of an application** without slowing it down.

With broad platform support that includes Compute Engine VMs, App Engine and Google Kubernetes engine. It allows developers to analyze applications running anywhere, including Google Cloud as our cloud platforms or on premise. With support for Java, Go, Python, and NodeJS.

Cloud profiler presents the call hierarchy and resource consumption of the relevant function, in an interactive flame graph that helps developers understand which paths consume the most resources. And the different ways in which their code is actually called.

## Why Monitor?

Why should we monitor? Perhaps this question has an obvious answer, but let's ask it anyway. Let's start by defining our terms. In Google's Site Reliability Engineering book, **monitoring is defined as collecting, processing, aggregating, and displaying real-time quantitative data about a system,** such as query counts and types, error counts and types, processing times and server lifetimes. There are many answers to the question, why monitor? 

- Continued system operations, 
- trend analysis over time, 
- building dashboards, 
- alerting personnel and systems violate predefined SLOs, 
- comparing systems and systems changed 
- providing data for improved incident responses are just a few. 

For reference, Google has published several books on SRE. They are available to read for free at landing.google.com/sre/books.

An application client sees the **product** and as a result, developers and business stakeholders both tend to think that the most crucial way to make the client happy is by developing a great product. 

But **developing the product is just the tip of the pyramid**. Great products need to be deployed into environments with **enough capacity to handle expected client load**. Someone needs the correct information to make those capacity planning decisions. Great products need thorough **testing**, preferably at least partially automated, and also preferably as part of a refined Continuous Integration, Continuous Development **CI/CD release pipeline**. Past that we also have to keep clients happy by building **trust**. Trust is gained by creating good products with good **up-time and SLO compliance**, but it also requires transparency when things go wrong. **Post-mortems and root cause analyses** are the DevOps teams way of letting the client know what happened and why it is unlikely to happen again. These are integral parts of robust instant response. All of this sits squarely on a solid base of monitoring systems and data.

Monitoring reveals things that need urgent attention and transient application usage patterns which can yield better capacity planning and generally help improve the client's experience endless and their pain. 

Why do we monitor? Because we want that **good, reliable, and trusted product we saw on the top of our pyramid**. We want our product to improve continually and we need the monitoring data to make sure that it does. 

We want **dashboards** to provide business intelligence. So our DevOps personnel have the data they need to do their jobs. We want automated **alerts** because a huge wall of monitors with someone staring at it, as is often shown in the movies, is a fallacy. Humans tend to only look when there's something important for them to look at. So it's better to construct automated systems to prioritize and handle as many alerts as possible.

Finally, we want monitoring systems to help provide data crucial to **debugging the functional and performance issues** of our applications. 

Continual improvement starts with robust **forecasting and trend analysis**. It answers questions like, what's our current database capacity and growth rate? Can current systems and infrastructure **handle** the holiday rush? What should we change if we want to expand and start handling new international client load? 

When parts for improvement are discovered, monitoring helps with testing changes and proposing improvements such as, would adding memcached improves site performance? How about the latest software update? Is it affecting performance? How would changing x impact capacity? 

Continual improvement related monitoring also provides data crucial for business application and security analysts, helping answer questions like, 

- how can we spot evolving customer needs? 
- How and where could software be improved? 
- Is someone trying to hack the system? 

Dashboards provide core metric visibility into your cloud resources and services with no configuration. Define user or job targeted custom dashboards and take advantage of Google's powerful data visualization tools. Dashboards can be **combined with automated alerts** to help avoid the need to have personnel stare at a huge wall of displays, even better many alerts signals could be handled by non-human automated systems. 

Configure alerting policies to notify you when events occur or a particular system or custom metrics violate rules that you have defined. Use multiple conditions to define complex alerting rules and receive notifications through one of several channels, including **e-mail**. This is good for informational messages, but be careful that alerts don't just become more spam. 

Tickets from some type of ticketing system. Goodwin alert needs to be handled by humans soon, but maybe not right now. One of the several pager type options, Goodwin a human interaction needs to happen sooner rather than later. In any system, bad things can happen and when they do, they need to be debugged and fixed. Typically there's some **triggering event,** a system outage, data loss, a monitoring failure, or some form of manual intervention. 

The **trigger leads to a response**, by both automated systems and DevOps personnel. Many time the response starts by examining signal data coming in through monitoring. The impact of the issue is evaluated and escalated when needed, and an initial response is formulated. Throughout, good SREs will strive to keep the customer formed and respond when appropriate. After the issue has been fixed or mitigated, a **postmortem** is created. It should contain documentation for both future DevOps personnel and the client. Included in the documentation where appropriate should be; a **thorough root cause analysis, and a list of preventative actions.**

Setting expectations. Monitoring isn't a job, so much as a skill, that multiple team members will need. There is no single do it at all tool. That's why this class covers multiple Google Cloud products. There is also no such thing as an infallible system. But there can be SLOs that make sense, and you can use these to build trust, through transparency, and quick fixes. Never forget the **KISS. Keep it Simple, Stupid principle**. When you're planning and implementing monitoring.

How do you eat an elephant? You eat one bite at a time. The same is true with monitoring. Focus on one system, one service at a time, and then divide and conquer your way to success. It's easy for monitoring to become too focused. It's better to monitor from multiple vantage points. 

**External monitoring** is helpful because it represents the **client's view**, but the data it can access is often limited. Testing from inside a service has more access to data in detail, but it lacks that client perspective, so use them both. In discussions related to monitoring and Google Cloud, the phrase, "single pane of glass" is often used and just as often is misunderstood. A single pane of glass does not mean, that all Google projects can be monitored from one enormous Monitoring Project. At most, a monitoring workspace can support 100 subprojects at this time. 

Unless your Cloud footprint is smaller than that, you will need multiple monitoring workspaces. A single pane of glass also does not mean that all metrics get displayed in a single view so that there's one huge view to rule the monitoring world. That would just be confusing. A single pane of glass certainly doesn't mean that someone gets to watch a wall of monitors all day. That's boring, impractical, and it's also ineffective.

What a **single plane of glass** does is deliver a **logical grouping of multiple projects into a single workspace**. If 15 teams create 15 services that all work together to form a contiguous whole, then yes, I could monitor those 15 production projects from a single monitoring workspace created as 16th Project. Single pane of glass can also mean it's possible to display **multiple charts in one dashboard**. It's possible to collect and show a correlation between monitoring signals across multiple apps, platforms, and services. Your monitoring system should address two questions.

**What exactly is broken and why?** The what's broken indicator indicates the symptom. Examples could be, a website that suddenly starts serving 500 HTTP status errors. A service that is showing, latency spikes, a file that should be private, but is in fact publicly accessible. A service that returns errors but only to UK-based clients.

**The "Why"** from our two questions that monitoring system should address, indicates, a **possible cause**. We hope our monitoring path ultimately leads us to the cause because that's what needs to be fixed or at least mitigated. The cause could be, that the database is down. That our Compute Engine VM resources pool has been exhausted. That the last code update inadvertently changed to storage objects permission, or that a piece of service code has a logic error.

When you're monitoring there are two crucial perspectives, **black-box monitoring is a symptom-oriented type**, and represents **active problems**. For example, the system is returning 500 errors right now. If tests externally accessible behavior as a consumer would, through the API interface.

**White box monitoring is based on metrics exposed** and collected by the internals of a system. It might include co-created logs or metrics. With its added visibility from inside the system being tested, **white box better allows detection of imminent problems, failures mass by retries, and so forth**. Best practice monitoring should combine the **heavy use of white-box monitoring with the modest but critical uses of black-box monitoring**. Note that in a multilayer microservice system, one services system is often another's cause.

## Critical Measures

Monitoring starts with **metrics**. 

Business decision-makers want to measure the value of projects so they can better support their most valuable projects while not wasting resources on those that are not beneficial to the business. A common way to measure success is to use metrics. Metrics can be categorized as **business metrics and technical metrics**.

In business, success metrics might include return on investment, ROI, earnings before interest and taxes, EBIT, employee turnover, and customer churn. In software, success metrics could be page views, user registrations, click-throughs, and checkouts. But you can't measure everything, so when possible you want to choose metrics that are **SMART, S-M-A-R-T.** 

Metrics should be **specific**, that's the S. "Hey everyone, is this site fast enough for you?" Is not really specific, it's subjective. "The 95th percentile results are returned in under 100 milliseconds", now that's specific. 

Metrics should be **measurable**. A lot of monitoring is numbers grouped over time with math applied. A metric needs to be a number or a delta, or something that we can measure in place in a mathematical equation. 

Metrics goals should be **achievable**. One hundred percent availability might sound good, but it's not possible to obtain, let alone maintain over an extended window of time. 

Metrics should be **relevant**. Does it matter to the user? Will it help achieve application related goals? If not, then it's a poor metric. 

Finally, metric should be **time-bound**. You want a service to be 99 percent available? That's fine, but is that per year, per month, per day? Does the calculation look at specific windows of set time from say, Sunday to Sunday or is it a rolling period of the last seven days? If we don't know, how can we measure it accurately? 

A good place to start metrics selection is with one or more of the four golden signals, **Latency, Traffic, Saturation, and Errors.** 

**Latency** measures how long it takes a particular part of the system to return a result. It's important because it directly impacts the user experience, because changes in Latency could indicate emerging issues and because its values may be tied to capacity demands. Also, it may be used to show or measure system improvements. Sample Latency metrics include page load latency, number of requests waiting for a thread, query duration, service response time, transaction duration, time until first response, and time until complete data was returned. 

**Traffic** measures how many requests are hitting your system. Traffic is important because it is an indicator of current system demand. Its historical trends are also used for capacity planning and as a core measure when calculating infrastructure spend. Sample Traffic metrics include number of HTTP requests placed per second, number of requests per static versus dynamic content, network I/O, number of concurrent sessions, number of transactions per second, number of retrievals per second, number of active requests, number of write ops, number of read ops, and number of active connections. 

**Saturation** measures how close to capacity a system is, with capacity being a subjective measure depending on the underlying service or application. It's important because it's an indicator of how full the service is. It focuses on the most constrained resources, and is frequently tied to degrading performance capacity is reached. Sample capacity measures include percent of memory utilization, percent of thread pool utilization, percent of cache utilization, percent of disk utilization, percent of CPU utilization, disk quota, memory quota, number of available connections, and number of users on the system.

**Errors** measure system failures or other issues. They are important because they may indicate that something is failing, they may indicate configuration or capacity issues, and they can indicate SLO violations. An Error might also mean it's time to send out an alert. Sample Error metrics include number of wrong answers or green correct content, number of 400 or 500 HTTP codes, number of failed requests, number of exceptions, number of stack traces, server fails, liveliness check, number of dropped connections.

## Four Golden Signals

### SLIs, SLOs, SLAs

Now that we know some general monitoring concepts, and since we've discussed the four golden signals. Let's take some time to lay a conceptual foundation in SLI's, SLO's and SLA's.

You're going to hear two terms a lot through the rest of this module, SLI's and SLO's. I'm sure many of you will have run into these terms before, but we're going to briefly discuss the concepts so we can be sure everyone is on the same page.

**Service level indicators are carefully chosen monitoring metrics** that measure one aspect of a service's reliability.

Ideally, SLI should have a close linear relationship with your users experience of that reliability. And we recommend expressing them as the ratio of two numbers. The number of good events divided by the count of all valid events.

**A service level objective combines that SLI with a target reliability**. If you express your SLI's as we recommend it, your SLO's will generally be somewhere just short of 100%. For example, 99.9% or as it's known, three nines.

We talk about users and customers interchangeably during the workshop. We have an expansive definition of who your users are. They can be internal or external to your company, humans, other companies, or even automated systems. Customers are the subset of your users who are paying directly for a service with real money.

Services need SLO's. It's quite a bold assertion, isn't it?

But this is why we're here. We want you to leave this module convinced that you should set SLO's for the services you're responsible for, with a solid understanding of how to do this in practice. You don't have to believe us about the value of SLO's. In Chapter 3 of The Site Reliability Workbook, you can read two case studies from large businesses that successfully introduced SLO's throughout organisations. The book is now freely available, so you won't even have to pay to do so.

SLO's also feature heavily in David Blank-Edelman's excellent book, Seeking SRE.

We've obviously cherry picked the quotes here. But these case studies demonstrate that SLO's bring long lasting improvements to your business. And they also provide blueprints and strategies for rolling out SLO's in your own company.

The most important feature of any system is **reliability**.

Not the reliability and availability while often related **do not mean the same thing**.

**Availability measures the ability of an application to run when needed**. While <u>reliability</u> measures the ability of that application to **perform its intended function for a specific time without failure**.

Why do we want you to believe that your services need SLO's? Well, it stems from this driving principle, that the most important feature of any system is its **reliability**.

You can easily lose the trust and respect of your users if service becomes too unreliable, or simply perceived as being too unreliable. Those countless hours you've spent designing and building the most amazing user experiences are all for nothing if your users can't access them.

Unreliability can have disastrous consequences for your business. Competitors will snap up your current dissatisfied users, and your poor reputation will slow, or even halt the acquisition of new users.

It's not enough to just declare that your services must be more reliable. If that worked, you wouldn't be here listening to us. Who has experienced a story like this? Quote, the development team for service decides to focus on feature iteration speed, adopting practices like Agile development and Push On Green to accelerate planning and release cycles. This increases the burden on service operators who are left scrambling to catch up with a steady stream of beta features making their way into production.

Inevitably something gets pushed before it's quite production ready, leading to a very visible outage and lots of customer shouting on social media. The operations team reacts by setting up procedures to protect the production environment from all of this terrible outage inducing change, slowing developers down and making them sad. And quote, reliability is a shared goal that many different parts of your organization must work towards together. In many companies, it's common to separate out the responsibility for operating a service from that of building new features for it.

This allows people in each role to specialize, but alters their priorities in a way that can often cause conflict.

To avoid this scenario, your organization needs to find a way to incentivize the developers and operators of a service to cooperate. So you can build new features without regularly suffering serious reputation damaging outages.

But you can't spend all of your engineering time on reliability, because reliability is not a standalone product. So the question you end up asking is, what is reliable enough?

Shortly followed by, **how can I objectively measure the reliability of my service**?

That's why we think your services need **SLOs**. We think of SLO's as a principled way to agree on the desired reliability of a service.

SLO's provide different value to different parts of an organization.

We've claimed a reliability is the most important feature. But for many product oriented folks, this brings up a difficult question.

When should engineering for increased reliability take priority over that shiny new feature you been designing for months? If you have an agreed upon and widely communicated target for service reliability, you can answer that question with objective data. So, SLO's provide a common language and shared understanding anchoring your conversation about service reliability on concrete data. It's tempting to consider SLO's be a purely operational concern. But for them to function correctly as a signal for prioritization of engineering work, your reliability targets must be set in conjunction with engineering and product teams. Everyone must agree that the target accurately represents the desired experience of your users.

What do we mean by reliable in the context of an Internet service? Let's start by talking about what it means for a service to be working well, or indeed just working.

- What should you be able to do?

- What characteristics are important to you?.

- What are your expectations of how the service should respond?


If your service has paying customers, you probably have some way of compensating them with refunds or credits when that service has an outage. Your criteria for compensation are usually written into a service level agreement, which describes the minimum levels of service that you promise to provide, and what happens when you break that promise. The problem with SLA's is that you're only incentivized to promise the minimum level of service and compensation that will stop your customers from jumping ship to a competitor.

Customers often feel the impact of reliability problems before these promises are breached. When the reliability falls far short of the levels of service that keep your customers happy, and contributes to your perception that your service is unreliable.

Compensating your customers all the time can get expensive. So what targets do you hold yourself to internally?

When does your monitoring system trigger an operational response? To give you the breathing room to detect problems and take remedial action before your reputation is damaged, **your alerting thresholds are often substantially higher than the minimum levels of service documented in your SLA**. SLO's provide another way of expressing these internal reliability targets. For SLO's to help improve service reliability, all parts of the business must agree that they are an accurate measure of user experience, and must also agreed to use them as primary driver for decision making.

Your customer's probably don't need to be aware of them, but they should have a measurable impact on the priorities of your organization.

**Being out of SLO must have concrete, well-documented consequences, just like there are consequences for breaching SLA's.**

For example, slowing down the rate of change and directing more engineering efforts toward eliminating risks and proving reliability will get you back into SLO faster.

Operations teams need strong executive support to enforce these consequences, and affect change in your development practice.

So how reliable is reliable enough?

One way of looking at an SLO is that it helps you answer this question.

When do we need to make a service more reliable? But really, the question is, where do we draw the line? Thus far all we've established is that your SLO target should be substantially higher than the reliability of SLA's promise. This doesn't help too many free services funded by advertising, that don't have user facing SLA's at all.

So how reliable is reliable enough?

How do you measure how far off that target you are? And what do you do if you've decided that you've missed that target?

You might think that you want to set your targets as high as possible to serve your users the very best you can. But this is not the correct way to set targets. The key realization that Ben Treynor Sloss, the founder of SRE at Google had was that **100% is the wrong reliability target for almost everything.**

This is even true for supposedly reliable things like pacemakers, which according to a paper published in 2005, actually had an average reliability of around 99.6% between 1990 and 2002.

Making a service more reliable requires increasing commitments of both engineering time and operational support for ever decreasing improvements to overall reliability.

At some point before you reach 100% reliability, that trade-off is no longer worth making because the costs outweigh the benefits. But how do you figure out where the optimal target is?

As an example, on the chart, you're given reliability levels in the left column. Take 99.95% as an example, and to the right are the allowed 100% outage durations.

In column two, you'll see that annually a system with a reliability level of 99.95%, could actually be down completely for total of 4 hours and 22 minutes and 48 seconds, before it violated its targeted reliability level.

For any given 28-day period, the same system with the same reliability target could be down 20 minutes and 10 seconds.

This red section shows cases where the total downtime for the given period is less than a single hour.

The smaller chart on the right further examines the 99.95% reliability target over a 28-day window. This chart compares the error percentage and how it affects the time before the SLO error budget is depleted.

If an error is only generated for 10 percent of the requests, the system could run for 3 hours, 21 minutes and 36 seconds before depleting its error budget.

A general guideline is adding a nine of reliability typically really raises the cost by a factor of 10, while providing only one 10th of the benefit.

We have a simple rule of thumb for where SLO target should be that generalizes the case. A typical user of your service should be just about as happy with the service when it operates at those targets. If the service was any less reliable, you'd no longer be meeting their expectations, and they would start to become unhappy. We call this the happiness test.

The challenge is quantifying and measuring that happiness, because measuring it directly tends to require the kind of invasive medical procedures that customers are often unwilling to consent to. When we talk about the happiness test, we say barely met because there are down sides to exceeding your reliability target too.

Once your users are happy with the reliability of your service, additional reliability has little value.

Maybe your users would be happier if you built new features, or made other service improvements with the resources that you've instead dedicated to far exceeding your reliability targets.

The worst part is if you've consistently well over targeted your SLO, your users will eventually come to expect a service to be that reliable all the time.

A common way for this to occur stems from factoring the risk of rare but damaging events into your SLO targets. If these events don't happen for a long time, through the combination of good operational response, and even better luck, you'll be in for a bad time from your users when they do finally occur.

Your users expectations form a kind of implicit SLO that you won't even know exists until they unexpectedly start complaining.

If we're not targeting 100% reliability, then whatever target we do set implicitly allows for a small number of errors to be served to users. If everyone agrees that SLO target represents the point at which users start to become unhappy with the reliability of the service, we can theoretically serve errors beyond that point without impacting user happiness.

**For example, if you set a three nines target, that means you can serve one error in every 1,000 requests to your users.**

Or in terms of complete downtime, your service can be unavailable for a little over 40 minutes in a four week period. Instead of just passively measuring the SLO and potentially exceeding it substantially, you can treat the acceptable unreliability as a budget that you can spend on various development and operational activities.

Figuring out how much budget you've got available from a percentage reliability target means specifying a time window for the events your SLO's are measuring. **Within Google, we often use fixed quarterly SLO windows for reporting purposes**. And we've seen this pattern at many of our customers' businesses too. **The problem with fixed calendar oriented windows is that they refill your error budge in one large step as the window rolls over**.

This makes them unsuitable for operational purposes. Your customers are still going to remember yesterday's massive outage on the first day of the new quarter, but your budget has been reset, so everything's fine, right?

**Instead, we use shorter rolling windows to drive decisions about operational priorities. Cloud services, use a 30 day rolling window, where we generally recommend 28-day rolling windows for the new SLO's.** Since it's exactly four weeks, there's no variation introduced by having five release days within the window when you're doing weekly software releases, or five weekends within the window.

We also use a **very short-term rolling window like one to 12 hours to drive alerting**. If you burn multiple hours of error budget within an hour, you can be relatively certain that an operational response is necessary.

When you've decided on the time window, count the number of events within that window.

Multiplying this by the SLO target yields the absolute minimum number of good events that must be served within that window to meet the SLO, which in turn tells you the allowable number of bad events, the error budget. When you subtract the actual number of bad events, you know how much error budget you have remaining.

This is a key signal to feed into your project planning cycle. If you have plenty of budget to spare, you can take more risks and move faster.

But if you've already used most of your budget, that's a signal that you need to start prioritizing and fixing those pesky bugs, instead of building that cool new stuff. If you are way over budget, your users are unhappy and you urgently need to start trading feature velocity for reliability work.

For those of you coming from a more structured environment, we found this approximation works well when trying to fit SLO's into the world of ITIL change management.

**ITIL by the way is a set of detailed and documented practices for managing IT services.**

When a service is in SLO, the developers and operators have much more leeway to make changes to the service without going through potentially lengthy review and approval processes, which is something ITIL calls a standard change.

If the service is running on error budget and looks like it may exhausted in the near future, those processes kick back in again to catch any changes that might result in burning the rest of the error budget. Confusingly, ITIL calls this normal change.

This all leads us to the question, **what should we spend our error budget on?**

For large services, we generally expect that some of it will be **burned by a background rate of failures,** but for making a **conscious decision to allocate this budget to development or operational activity**. What kind of activity are we talking about?

At Google, we've observed that the two biggest factors that burn error budget are software releases and configuration changes. This is why we often recommend that a reasonable consequence of **burning all your error budget is to slow down, or even completely stop making non essential changes to a system until the service is back within SLO.**

Of course, sometimes things just break. And it's a good idea to make sure you keep some slack in your budget allocations to account for emergencies and unforeseen circumstances.

If you have to take a service down for essential maintenance, you can pay for it with downtime from your error budget. Although if the downtime is communicated far enough in advance, your user should be expecting it, and therefore, less unhappy about it.

Lastly, one useful thing to spend small amounts of error budget on is **experimentation**. The ideal is that your **SLO target represents the dividing line between happiness and unhappiness**. But how do you know you draw that line in the right place.

If you have other ways of gauging the happiness of your users, you can design experiments to test this. For example, you can set things up so a small group of users are served exactly at SLO, and observe whether their happiness metrics decline.

### Choosing a good SLI

So how exactly do you choose a good SLI?

To show how this works in practice? Let's consider the interval highlighted in red, where we know our users were unhappy, because we saw many more support requests during that time.

You might be wondering **why we can't use request for support as an SLI directly**. Here are a few reasons. 

- **First, it's a trailing indicator**. While it does accurately measure unhappy users, it's of no use if we want to proactively prevent those users from becoming unhappy.
- Second, many users who are unhappy with the service **will just silently stop using it, because the burden of filing a support ticket is too high**. We need to capture those users in any SLI we recreate.

- Third, users **don't need support when the service is working correctly**. This means we get no signal at all from this SLI most of the time. So we can't base an error budget on it.


Well we've got plenty of monitoring metrics we're already collecting about our services. Maybe some of those changed during this time period, and we can use them as SLIs. Like most of you we've got systems metrics like load average, CPU utilization, memory usage and band with graft invisible on our monitoring dashboards. Sharp changes in these are often associated with outages, but they don't make for good SLIs. If you think about it, from the perspective of your users, they don't directly see your CPUs pegged at 100%. They see your service responding slowly. The same goes for internal service metrics like thread pool fullness and request queue length.

So **what properties of a metric make it good for the use as an SLI**? Let's assume we've trawl through our metrics and come up with these two candidates. If we had to choose between them, why would we say that the metric on the left is bad for use as an SLI, while the metric on the right is better?

While our bad metric does show an obvious downward slope during the outage period, there's a large amount of variance and the expected range of values seen during normal operation, shown in blue on the left, has a lot of overlap with the expected range of values seen during an outage. In red on the right, this makes the metric a poor indicator of user happiness.

In contrast, **our good metric has a noticeable dip that matches closely to the outage period**. During normal operation, it has a narrow range of values that are quite different from the narrow range of values observed during an outage. The stability of the signal makes overall trends more visible and meaningful. This makes the metric a much better indicator of user happiness.

This matters because **SLIs need to provide a clear definition of good and bad events**. And a metric with **lots of variants and poor correlation with user experience,** is much harder to set a meaningful threshold for.

Because the bad metric has a large overlap in the range of values. Our choices are to set a tight threshold and run the risk of false positives, or to set a loose threshold and risk false negatives. Worse. Choosing the middle ground means accepting both risks.

The **good metric is much easier to set a threshold for because there is no overlap at all.** The biggest risk we have to contend with is that perhaps the SLI doesn't recover as quickly as we might have hoped for after the outage ends.

Summing it up, we want our SLIs to be things we can measure about our system. That also correlate well with the happiness of our users.

We generally recommend that you **express your SLIs as the proportion of a set of events, that were considered to be good.**

Expressing all your SLIs in this form has a couple of useful properties. 

**First, your SLI fall between 0 and 100%**. Where 0 means nothing works and 100% means nothing is broken. The scale is intuitive and directly translates to percentage reliability SLO targets and error budgets.

**Second, your SLI have a consistent format which can serve as an interface for abstraction**. Consistency allows common tooling to be built around your SLIs, alerting logic, error budget calculations. And SLO analysis reporting tools can all be written to expect the same inputs, good events, valid events and your SLO threshold.

But what do we mean by valid and why don't we use all events in the SLI equation?

Sometimes you may need to exclude some events recorded by your underlying metrics from being included in your SLI, so they don't consume your error budget.

A good example here might be ignoring 300 and 400 HTTP response codes as irrelevant to your services' SLO performance. This freezing allows you to make this exclusion explicit and specific, by declaring those events to be invalid.

### Specifying SLIs

Now let's talk about specifying SLIs.

Let's start with an example application Fang Faction and the game players build and manage a post apocalyptic settlement. Recruit survivors to their faction and wage war on other players, like many games and makes revenue by selling in game currency for real world money, which players can then use to skip time gates, upgrade buildings and get more recruits.

The serving infrastructure is relatively simple. There are two data stores, a set of API servers and web servers, and a set of game servers with their own databases, all sitting behind a layer 7 HTTP load balancer.

The game has both a mobile client and a web UI. The mobile client mixed requests to our serving infrastructure via JSON RPC messages transmitted over restful HTTP. It also maintains a web socket connection to receive game state updates. Browsers talked to the web servers via HTTPS.

Leaderboards are updated every five minutes.

What are the users expectations when they try to load this page? I'm sure you can imagine a number of user journeys for this game. We're going to keep things simple for now so we can step through the process without too many complicating factors. Our game allows players to log into their account via web browser and keep track of their settlement while they are actively playing on their device. Here you can see a mock up of what a player sees when they log into the games website and open their profile page. On the right is a sequence diagram showing the requests and responses involved in serving this page to them. You can also find this on page 18 of the handout attached to this lesson.

The dotted lines are requests that are not observable from our serving infrastructure. What do you think their expectations are for this page? What would disappoint them and make them think the service is not reliable?

Here's how our infrastructure serves a page. The player's HTTPS request is terminated at our load balancers which forwards it to a pool of web servers.

That server looks up the player's profile in the user profile data store.

The profile contains most of the information displayed on the profile page, including the player score and current location of their settlement. The web server requests the leaderboard for that location for the leaderboard data store then builds the HTML response for the player and sends it back. When the players' browser subsequently request their avatar, this request is served in much the same way.

**A user journey is a set of interactions a user has with the service while achieving a single end result.**

Similar to critical user interactions or CUIs from user experience UX research.

In our game, each user journey represents a discrete set of actions a user could perform, such as View Profile Page.

This user journey starts with a user's web browser initiating a connection to the web servers. If fetches content from a content delivery network, CDN and may allow the user to upload a new avatar photo.

**Applications have many user journeys and each journey will have separate measurable metrics** that users likely already use subconsciously, to rate the reliability of your application or service. In this lesson, you will learn how to identify these metrics and use them to ensure that your user experience remains within acceptable bounce.

It may be tempting to rush off and start looking at which metrics might make good SLIs for your services. But we've got one more recommendation for you first.

In general, your users are using your service to achieve some set of goals. So your **SLIs must measure their interactions with your service in the pursuit of these goals.**

You should aim to **have around 3-5 SLIs covering each user journey**, even if your system and your user journeys are relatively complex.

A service with too many SLIs places an additional cognitive burden on the people operating that service, especially if those SLIs start to contradict each other. We're not recommending that you discard all of your other monitoring and observability systems after you start measuring SLIs. The way we think about it is that a deterioration in your SLIs an indication that something is wrong. When that problem has become bad enough to provoke some sort of incident response, you'll need those other systems to debug and help you figure out what that is.

To begin figuring out what SLI is you should have for each journey, ask yourself the following questions. 

- What are the users **expectations** of the **reliability** of the service?
- How can we **measure** the users experience versus those expectations with our **monitoring systems**?

- How does the user **interact** with the service?


The SLI menu we're showing you here is a good place to start, if you're not sure what kind of SLI is you should measure for a particular user journey. It's also on page six of the handout attached to this lesson so you can refer to it later.

**Request/Response**

- Availability
- Latency
- Quality

**Data Processing**

- Coverage
- Correctnes
- Freshness
- Throughput

**Storage**

- Throughput
- Latency

### Developing SLOs and SLIs

Now let's take our SLI measures and develop them into SLOs and SLIs. 

An **SLO** is supposed to represent the **line at which users become unhappy with the service**. Once you're sure you have a good SLI, one that has a close predictable relationship with the happiness of your users, what reliability targets should you set? 

How do you figure out where to draw the line? 

The ideal is that your **reliability targets reflect the needs of your business**. As we said earlier, being too reliable has cost. If you're services are amazingly reliable, but your users would be happy with two-nines because it's failure modes are acceptable, maybe that means you can take more risks and shift features faster. Not being reliable enough is often more costly. What if the only thing keeping your users with you is that your competitors reliability is even worse than yours for now. 

We call SLO based on business needs, **aspirational SLOs** because it's entirely reasonable for you not to be able to meet them initially. Over time with some engineering effort, this is the level of reliability that your operations development, product, and executive functions want to reach, that represents your best guesses at what level of reliability is right for your services, customers, and business over the long term. 

But if you have or can derive historical data for your SLIs, we recommend that you look at this data and **base your first SLOs on the past performance of your service**. Starting from this point, means that you can reasonably be sure of your ability to meet the SLO over the short to medium term. If your users are happy enough with your service at the moment, you can be reasonably sure that this SLO is not set too low. 

We call SLOs based on past performance, achievable SLOs since you should set the SLO threshold so you can expect to meet it most of the time. Just remember **that the past performance does not indicate future reliability**, the data you're basing your judgments on may not be representative of the long-term reliability achievable by your service. The difference between these two targets is a useful signal. 

If the business needs better performance than your services currently capable of achieving, that's a problem, but it's common for there to be some divergence when you're just setting out on your SLO journey. What's needed is some way to drive convergence between the two targets over time. 

Aspirational SLOs are best guesses at what the business thinks makes the user happy and achievable SLOs are making the assumption that current performance makes the users happy. To validate these assumptions, you need to find some **external signals that indicate the happiness or discontent of your user base. Good examples of these are support requests, forum complaints, and Twitter.**

Understanding any discrepancies between the signals of user happiness and your SLOs particularly in instances where your users were sad but you are still within your SLO, will help you refine your SLOs targets. 

**Significantly over-performing your SLO means that you haven't error budget to spend**, maybe you can safely take on more risks. 

**Significantly underperforming your SLO may mean that you're taking too many risks** or if your users are happy, you could react your SLO targets.

When you're just starting out, checking your SLOs against these signals more frequently is a good idea. You don't want to wait a whole year to find out that the first shot at setting reliability targets was way off. As you gain more confidence that your targets are in the right place, **you can revisit them less frequently, but we recommend doing so at least once a year**. 

A lot can happen in a year. Your user base may grow dramatically or your business might pivot to new markets with different requirements. In general, people use your service to achieve some set of goals. So the SLIs for that service must measure the interactions they have with the service in pursuit of those goals. 

An **SLI specification** is a formal statement of your users expectations about one particular dimension of reliability for your service, like latency or availability. 

The SLI menu gives guidelines for what dimensions of reliability you probably want to measure for a given user journey. When you have SLIs for specified for a system, the next step is to **refine them into implementations** by making decisions around measurement, validity and how to classify events as good. 

To begin figuring out what SLIs we should have for this journey, we need to ask ourselves, what are the **user's expectations of the reliability of the service**? How can we measure the user's expectations versus those expectations with our monitoring systems? How does the user interact with the service? The SLIs menu we're showing you here is a good place to start, if you're not sure what kind of SLIs you should measure for a particular journey.

This is a Request/Response interaction, so we almost certainly want to measure the **availability** and **latency** experienced by players when they load the profile page. Put another way, they expect a profile page to load successfully and quickly. But these expectations raise questions. What does successfully and quickly really mean? It's important to be precise about the SLI definition.

Your **SLI should be clear about where it is measured, what is being measured, including any units used, and what attributes of the underlying monitoring metrics are excluded or included?** How can we define an SLI that answers these questions? Our first problem is that our web servers are way more than just profile page requests, with only those requests that are part of this particular user journey.

Let's define what attributes of a request make it a valid for inclusion in our SLIs. 

We identify profile page requests from the HTTP requests path, which we know from our sequence diagram is either /profile/user or /profiles/users/avatar. We also know the user's browser since HTTP GET requests to these paths. Let's substitute those definitions in. Here I've chosen to include avatar images in the availability SLI, but exclude them from the latency SLI. Avatar images are uploaded by users, and we don't know in advance how large they'll be. We can realistically assume that images will take longer to load than HTML. This means including avatar image latency into our SLI would make the underlying latency distribution bimodal and introduce a lot of variance. If you remember what we talked about earlier, this could cause problems when we're trying to set a latency SLO target, because we can't accurately target just one of the two loading times. Our users might be unhappy with the 500 millisecond increase in page loading time, but that can still be less than the average time it takes to serve an avatar image, which they're still happy with an aggregate. Since the page is still broadly usable even before the avatar image loads, page loading time is the metric we care about more. That's where we'll base our SLI on.

Next, we need to be **more specific about what successfully and quickly really mean.** Our company just wants to get something simple measured quickly. We're going to use the HTTP status code as an indicator of success. The latency threshold is a bit harder. If you have historical data, it's common to choose the cut off point where requests are too slow in tend with the SLO target that specifies what percentage of those requests are allowed to be slower than the cut off. 

The company has historical data for this particular endpoint. That's what we're going to do here with this x milliseconds construct. That makes this SLI implementation incomplete. You need a threshold to convert the latency distribution to a binary good, bad signal. We'll be asking you to figure out what thresholds are appropriate in a bit. Instead of enumerating every possible code for availability SLI, we'll assume that any 500 class codes are bad and exclude 429 from our good requests too because it's what our servers returned to users when they're overloaded. But there's still one piece of the puzzle missing. 

We haven't said how or where we're going to measure any of this. 

Broadly speaking, there are **five ways to measure an SLI and none of them are perfect**. Making informed engineering decision on which measurement strategy to use based on the current needs of your service is an important part of refining your SLI specification into concrete implementation. It's entirely reasonable to use more than one strategy for each specification. For example, you may choose to use **real-time metrics from your load balancing infrastructure** to drive short-term operational response, but base your long-term SLOs on reconstructing user journeys from session IDs in the server side blocks.

In this case, the company decided that the metrics provided by their load balancer were good enough to establish some initial SLIs without investing too much engineering effort. This is an entirely reasonable trade off to make. Your first SLI are almost certainly going to need some improvement, so the sooner you start gathering data and begin the cycles of iteration the better. Those final SLI implementations provide a lot of explicit detail about what exactly i-s being measured and how it's being measured. If you give them to someone that could actually build something and evaluates these metrics and give you performance data.

This example outage illustrates why you should take a critical look at your infrastructure when creating your SLIs. When we're considering the failure modes of our infrastructure, we're trying to answer **four questions**. 

- Can we measure the SLIs we've created where we want to measure them given this Infrastructure? 
- Do the SLIs adequately capture the user journey and its failure modes? 
- Are there any exceptions or edge cases that we need to consider? 
- Do the SLIs capture multiple journeys with differing requirements? 

We say adequately capture because reaching a 100 percent coverage of all possible failure modes is again the wrong target. **Distributed systems fall in complex and surprising ways**, so a 100 percent is an unrealistic goal. If the failure mode is sufficiently rare that it won't eat more than a fraction of your error budget, it's reasonable to argue that it's safe for your SLIs not to capture it. 

Failure modes that are outside of your control and your users are unlikely to blame you for are best left out of your SLIs. Things like failures that consumerize or mobile phone carriers would fall into that bucket. You can't do much about them, but in some cases it can be useful for your support teams to know that they have happened. You also have to perform some cost-benefit analysis. In some cases, extending your SLI to cover particular failure mode could cost months of engineering effort and require re-architecting your service, your monitoring systems or both when building detailed client-side instrumentation from scratch. What targets do you think are justifiable given the previous six weeks of data? What measurement window would you measure this target over?

## Developing an Alerting Strategy

Let's start by getting an alerting strategy in place.

Let's start by defining our term. And **alert is an automated notification set by Google Cloud through some notification channel** to an external application, ticketing system or human being. Why is this alert being set? Perhaps the services down, or an SLO isn't being met. Regardless, and alert is generated when something needs to change.

A great time to generate alerts is when a system appears to be on track to spend all of its error budget **before the allocated time window.** Remember from our SLI, SLO discussion in the last module, that an error budget is perfection minus the SLO. SLIs are the things that are measured, and SLOs represent achievable targets. If the SLO target is 90% of requests must return in 200 milliseconds, then the error budget is 100% minus 90% equals 10%. The events are processed through a time series, a series of event data points broken into successive, equally spaced windows of time. Based on need, each windows duration and the math applied to the member data points inside of each window are both configurable. Thanks to the time series, events can be summarized. Error rates calculated and alerts can be triggered where appropriate.

Several attributes should be considered when attempting to measure the accuracy or effectiveness of a particular alerting strategy. **Precision** is the portion of alerts detected that were relevant to the sum of relevant, irrelevant alerts. It is penalized by **false alerts.** 

**Recall** is the portion of alerts detected that were relevant to the sum of relevant alerts and missed alerts. It is penalized by **missing alerts**. Precision can be seen as a measure of exactness, where recall is a measure of completeness.

**Detection time** can be defined as how long it takes the system to notice an alert condition. Long detection times can negatively impact the error budget. But alerting to fast may generate false positives. Reset times measures how long alerts fire after an issue has been resolved. Continued alerts on repaired systems can lead to confusion.

Error budgeting 101 would state that when the error count is greater than the error budget, an alert should be generated. An SLO of 99.9% would be meaningless without a time period over which errors and events were counted. A window is the period of time the error calculation is made over. So if the SLO is 99.9% and the window is 30 days, then the error budget is 0.1% for every 30 days. One of the alerting decisions you or your team will have to make is the window length. The window is a regular length subdivision of the SLO is total time. Imagine you set a Google Cloud spend budget of $1,000 a month. When would you like to receive an alert? When the $1,000 is spent? Or, when the predicted spend is trending past the $1,000, of course the latter. Now the same concept, but this time imagining 99.9% SLO over 30 days. You don't want to get an alert when your error budget has already gone. Because by then it's too late to do anything about the problem.

One option would be small windows, **smaller windows tend to yield faster alert detections**. Shorter reset times, but they also tend to decrease precision because of their tendency towards false positives. In our 99.9% SLO over 30 days, a 10 minute window would alert in 0.6 seconds in the event of a full outage. It will consume only 0.02% of the overall error budget.

**Longer windows tend to yield better precision**, since they have longer to make sure that an error is really occurring. But reset and detection times are also longer. That means you spend more error budget before the alert triggers. And are saying 99.9% SLO over 30 days, a 36 hour window would alert in 2 minutes 10 seconds in the event of a full outage, but will consume 5% of the error budget.

One step towards faster detection and higher precision is the **addition of duration**. The error is spotted quickly but treated as an anomaly until the duration reached. This is what you do when your car starts making a sound. You don't immediately freak out, but you pay attention and try to determine if it's a real issue or a fluke.

The downside is, the **precision typically has an inverse relationship to recall.** As the precision goes up, you avoid false positives. You let the problem continue to happen. If the pay attention but don't alert yet duration is 10 minutes, a 100% outage for 5 minutes wouldn't be detected. As a result, if error spike up and down they may never be detected.

So how do we get good precision and good recall? **Multiple conditions**, there are many variables that affect good alerting strategy, including the amount of traffic, the error budget, peak and slow periods to name a few.

The fallacy is believing that you have to choose a single option, define multiple conditions in our learning policy to get better precision, recall, detection time and rest time.

You can define multiple alerts through multiple channels, perhaps a short window condition generates an alert. But it takes the form of a pub sub message to a Google Cloud run container, which then uses complex logic to check multiple other conditions before deciding if a human gets a notification.

And alert should always be prioritized based on the customer impact and the service level agreement. Don't involve the humans unless the alert meets some threshold for criticality. High priority alerts might go to slack, SMS, or even a third party solution like pager duty. Low priority alerts might get logged, sent through email, or inserted into a support ticket management system.

## Creating Alerts

Okay. We've discussed some of the alerting concepts and strategies. Now, let's run through the Google Cloud mechanics of creating alerts. 

Google Cloud defines alerts using **alert policies**. An alert policy has a name, one or more alert conditions, notifications, and documentation. For the name, use something descriptive so you can recognize alerts after the fact. Organizational naming conventions can be a great help. 

The **alert condition** is where you'll be spending the most alerting policy time and making the most decisions. This is where you decide what's being monitored, and under what condition an alert should be generated. You start with the target resource and metric you want the alert to monitor. You can filter, group by, and aggregate to the exact measure you require. Then the yes/no decision logic for triggering the alert notification is configured. It includes the trigger condition, threshold, and duration. If needed, you can set the aligner, alignment period, and even a secondary aggregator. 

**Aligners** are the math applied to each alignment period in the time series. The alignment period determines how frequently the aligner is applied. To try and maximize both precision and recall with a single alert, multiple conditions that can be created. 

The policy **trigger** is used to determine how more than one trigger will relate to one another, and the alert triggering itself. The notification channel or channels decides how the alert is sent to the recipient.

Email alerts are easy and informative, but they can become notification spam if you aren't careful. SMS is a great option for fast notifications, but choose the recipient carefully. 

Slack is very popular in support circles. 

Google Cloud's Mobile app is a valid option. 

PagerDuty is a third-party on-call management and incident response service. 

Webhooks and Pub/Sub are excellent options when alerting to external systems or code. 

An alert may have zero to many notification options selected, and they can each be of a different type. The documentation option is designed to pass the alert recipient additional information they may find helpful. The default alert content will already contain information about which alert is failing and why. So think of this more like an easy button. 

If there's a standard solution to this particular alert, adding a reference to it here might be a good example of proper documentation inclusion. Then again, if it was that easy, automate it. Once one or more alert policies have been created, the alerting UI provides a summary of incidents and alerting events. An event occurs when the conditions for an alerting policy are violated. 

When an event occurs, Cloud Monitoring opens an incident. In the Alerting window, the Summary pane lists a number of incidents while the Incidents panel displays the ten most recent incidents. Each incident is in one of three states. 

**Open incidence**; if an incident is open, then the alerting policy set of conditions is currently being met or there's no data to indicate that the condition is no longer met. This usually indicates a new or unhandled alert. 

Next, there's **acknowledged incidents**. A tech spots a new open alert, but before they start to investigate, they mark it as acknowledged as a signal to others that someone is dealing with the issue. 

Finally, there're **closed incidents**. If an incident is closed, the alert policy conditions are no longer being met. An incident is listed as closed if there is no data to indicate whether the condition still holds and the incident has expired. 

The **Events** pane of the Alerting dashboard displays the most recent events, and includes a graphical indicator of the alert status, a link to the event details, a quick description, and a timestamp. You can monitor the availability of a resource by creating an alerting policy that creates an incident if the uptime check fails. You also have the option to observe the results of the uptime checks in the Monitoring Uptime Check dashboard. 

You can monitor the availability of a resource by creating an alerting policy that creates an incident if the uptime check fails. You also have the option to observe the results of uptime checks in the Monitoring Uptime Check dashboards. 

Logs-based metrics or Cloud Monitoring metrics based on the content of log entries. For example, the metrics can record the number of log entries containing a particular message, or they can extract latency information reported in log entries. You can use logs-based metrics and Cloud Monitoring charts and alerting policies. 

As we've covered earlier in this module, an **alerting policy describes a set of conditions that you want to monitor.** When you create an alerting policy, you must also specify its conditions, what is monitored, and when to trigger an alert. The logs-based metric serves as the basis for an alerting condition. 

**Groups** provide a mechanism for alerting on the behavior of a set of resources rather than on individual resources. For example, you can create an alerting policy that is triggered if some number of resources in the group violates a particular condition, for example, say CPU load, rather than having each resource inform you of violations individually. 

Groups can also contain subgroups up to six levels deep. One application for groups and subgroups is the management of physical or logical topologies. For example, with groups, you can separate your monitoring of production resources from monitoring of your test or development resources. You can also create subgroups to monitor your production resources by zone. 

**Resources** can belong to multiple groups, and given monitoring workspaces can have up to 500 groups. You define the one-to-many membership criteria for your groups. A resource belongs to a group if the resource meets the membership criteria of the group. Membership criteria can be based on resource name or type, network tag, resource label, security group, region, App Engine, app, or service. 

Resources can belong to multiple groups. Once a group is created, all the resources in the group can be monitored together as a unit.

## Creating Alerting Policies with the CLI

Alerting policies can be created in both the Google Cloud console and using the CLI or API. Creating alerts from the CLI or the API starts with an alert policy definition in either a JSON or a YAML format. One E-learning trick when learning the correct file format, is to create an alert using the Google Cloud console. 

Then use the gcloud monitoring policies list, and then the describe command to see the corresponding definition file. The alerting API and gcloud can create, retrieve, and delete alerting policies. As an example to create an alerting policy using gcloud, define it using JSON syntax and save it to a file. 

Then run the gcloud alpha monitoring policies create along with the remaining syntax, in order to execute this command. 

Now, let's examine some policy file examples. Our first example will be a Metric Threshold Policy. A metric threshold policy detects when some value crosses a specified boundary. Threshold policies let you know that something is approaching and an important point, so you can take some action. 

For example, when available disk space falls below 10 percent of your total disk space, your system may soon run out of disk space. This sample policy uses average CPU usage as an indicator of health of a group of virtual machines. It averages by instance ID per zone. 

Then it grabs the value with the highest CPU usage, and compares it to the 90 percent threshold. If it's over 90 percent threshold for more than 15 minutes, an alert is triggered. In our next example let's take a look at the rate of change policy. In this example we want to trigger an alert if the rate of CPU utilization is increasing rapidly. 

This time we filter for the GCE instance, CPU utilization metric. The align percentage change aligner averages CPU utilization over a 10 minute window. Here we take the window average from 15 minutes, or 900 seconds ago, and we compare it against a window for now, calculating a percent change. If that percent change is over 50 percent for 3 minutes, or 180 seconds or more, then trigger an alert. 

This time we filter over the GCE instance CPU utilization metric. The align percentage change aligner averages CPU utilization over a 10 minute window. Here we take the window average from 15 minutes or 900 seconds ago, and we compare it against a window for now calculating a percentage change. If that percentage changes over 50 percent for 3 minutes, or 180 seconds or more, then trigger an alert. 

Lastly, let's examine an uptime check, and its corresponding alert policies starting with the uptime check itself. Here we have an HTTPS uptime check on the Google Cloud homepage. It checks availability every 5 minutes, or 300 seconds by hitting the index.html page sitting on port 443 of cloud.google.com. The page has 10 seconds to respond, or the check will fail. 

To create the corresponding alerting policy for the last slides up time check, refer to the uptime check by its uptime underscore check underscore ID. This ID is set when the check is created. The ID appears as the last component of the name field, and is visible in the UI as the check ID and the configuration summary. 

The ID is derived from the display name and can be verified by listing the uptime checks and looking at the name value. The ID for the uptime check previously described its uptime check for Google Cloud site. This alerting policy example triggers if the uptime check fails. For some more policy examples, feel free to visit the links listed on this page.

## Service Monitoring

Now that we've examined alerts, their use in their creation, let's see how Google's new service monitoring console and API can help. Modern applications are composed of multiple services, and when something fails, it often seems like many things fail at once. 

To help manage this complexity, service monitoring helps with SLO and alert creation. Accessible through the Google Cloud Console via an API, service monitoring supports latency and availability-based SLI metrics. SLO performance goals can be specified and when combined with compliance periods, automated alerting is easily configurable. 

Service monitoring also calculates and reports error budgets to help with change planning. The service monitoring consolidated services overview page is your point of entry. Near the top of a page, a summary of your alerts and SLOs are displayed. 

Below is a summary view of the health of your various services. Here you can see the service name, type, SLO status, and whether any SLO related alerts are firing. To monitor or view details for specific service, click on the service name. You can apply filters to control which services are displayed in the table. 

First, you can click a filter by link in the SLO status section to display only the applicable services in the table. For example, you can filter the table to show only the services that currently have SLO alerts firing. 

You can also filter by entering a value in the filter table in the top-left corner of the table to apply additional conditions. Clicking on an individual service on the services overview page, will drill into its detail. There you can see existing SLOs, and by expanding them, their details. The SLIs current status, the error budget remaining, and the current level of SLO compliance are all displayed. If alerts have been set, their status is also displayed. 

As we discussed earlier in the module, error budgets are 100 percent SLO percentages. For example, if a service returns one error about every 1000 requests, then it's operating at 99.9 percent availability. If we determine that customers will tolerate 99.5 percent availability, and that's where we've set our SLOs, then that gives us an error budget of five percent. In this example, what portion of our error budget are we using currently? Twenty percent. The error budget is five percent, we're currently getting errors about one percent of the time, that means we've used one out of five or 20 percent of our error budget. 

**SLOs and error budgets are measured over a period of compliance**. That is a period over which the SLI performance is tracked. There are two fundamental types of compliant periods. 

First, there's **calendar-based periods,** which run from a fixed date to a fixed date. Perhaps you build customers on the first of the month, so you track SLOs and their error budgets from the first of the month to the first of the next month. 

There's also **rolling window-based periods**, which measure across a constantly moving window of time. Perhaps we always want to be in compliance over a sliding window containing the last seven days of data. There are two fundamental ways service monitoring can approach SLO compliance calculations. Request-based SLOs use a ratio of good requests to total request. For -example, we want a request-based SLO with a latency below 100 milliseconds for at least 95 percent of requests. So we'd be happy of 98 percent of requests were faster than 100 milliseconds. 

Window-based SLOs use a ratio of the number of good versus bad measurement intervals or windows. Each window represents a data point rather than all the data points that comprise the window. For example, take a 95th percentile latency SLO, that needs to be less than 100 milliseconds for at least 99 percent of a 10 minute window. Here, a compliant window would be a 10 minute period over which 95 percent of the requests were less than 100 milliseconds. We'd be happy if 99 percent of the 10 minute windows were compliant. 

Let's look at another pair of window-based versus requests-based SLO examples. Imagine you get one million requests a month, and your compliance period is a rolling 30 days. If you were looking for a 99.9 percent request-based SLO, that would translate to 1000 total bad request every 30 days. On the other hand, a 99.9 percent windows-based SLO average across one-minute windows would allow a total of 43 bad windows or 43,200 total windows times 99.9 percent, that would equal 43,157 good windows. 

**Windows-based SLOs are good and bad because they can hide burst-related failures.** If the system returns nothing but errors, but only every Friday morning from nine o'clock to 9:05, then you'd never violate your SLO. But no one would want to use the system that first thing Friday mornings.

Service monitoring makes SLO creation easy. On the services overview page, select one of the listed services. If a service is built on a Google Cloud Compute technology that supports service monitoring, then it will automatically be listed. Next, press the Create an SLO button. Select an option from the SLI Type drop list. At the time of this writing, service monitoring supported availability and latency-based SLIs. Now, availability is a ratio of the number of successful responses to the number of all responses, whereas latency is the ratio of the number of calls that are below the specified latency threshold to the number of all calls. In the SLO goals section, enter a percentage in the compliance target field to set the performance target for the SLI. 

Service monitoring uses this value to calculate the error budget you have for this SLO. In the compliance period section, select a period type and the period length. You will recall from earlier that the two compliance period types are calendar-based and rolling. Optionally, select Add a Windowed SLI. As discussed, a windowed SLI can help you catch or ignore periods of time when the service won't meet the SLO compliance target. Once the SLO is being created, it's easier to monitor the SLI current status, error budget, compliance, and alert status. Creating an alert is as easy as pressing the Crate Alerting Policy button. 

Service monitoring can trigger an alert when a service is on track to violate an SLO. The alerting policy can be based on the range of consumption of your error budget. You specify a look-back period, say the last 60 minutes of time, and an error budget consumption percentage over that period. Service monitoring will take care of the rest of the alert policy settings automatically. Determining what values you should set for the look-back period and in consumption percentage might take some trial and error. 

You could use the default look-back period of 60 Minutes as a starting point. To determine the consumption percentage, monitor the service behavior to see what percentage of the total error budget over the compliance period was consumed in the last 60 minutes. You want to set the consumption period so that you don't burn more error budget in the look-back period than what you can afford, but you don't want to set off an alert unnecessarily either. For example, suppose you created an SLO with the following name; 95 percent less than 300 milliseconds latency in account or week. With this SLO, only five percent of the total number request in a week can have a latency that's greater than 300 milliseconds. Heating or exceeding five percent consumes your total error budget. 

If you set the look-back period to one hour, each look-back period is one-168th of compliance period. There are 168 hours in a week, by the way. To calculate the hourly consumption percentage that doesn't exceed the total error budget for the week, use five percent divided by 168, is approximately 0.3 percent. Because latency for your service can fluctuate depending on load or other conditions, setting 0.3 percent as the consumption percentage might trigger unnecessary alerts. You could start with a value twice that, or 0.6 percent, then monitor your service and adjust the value as needed.

# Monitoring Critical Systems

## Observability Architecture

Let's start with the **observability architecture**. Google Cloud monitoring uses workspaces to organize monitoring information. A workspace is a tool for monitoring resources contained in one or more Google Cloud projects. It offers a unified view or a single pane of glass through which those resources can be watched. With the ability to monitor resources in the current and up to 100 other projects, **monitoring workspaces offers excellent cross-project visibility**.

The monitored resources may be part of **Google Cloud or AWS**.

Monitoring workspaces helps organize your monitoring efforts. They serve as a central, secured access hubs for monitoring information, dashboards, alerting policies, and uptime checks. This information is available, IAM permitting to both operations and developer personnel.

A **workspace is wrapped by a Google Cloud host project**, which the workspace always monitors. However, you can configure a workspace to monitor up to 100 other Google projects and AWS accounts. This allows you to create a dedicated monitoring projects to host project workspaces and thus expand your single pane of glass outside the current project.

A **workspace belongs to a single host project.** The host project stores all of the configuration content for dashboards, alerting policies, uptime checks, notification channels, and group definitions that you configure. If you delete the host project, you also delete the workspace.

The name of the workspace is set as the name of the host project, this isn't configurable. Since it's possible for one workspace to monitor multiple projects, but a project can be monitored from only a single workspace. You will have to decide which workspace to project relationship will work best for your organizational culture and this particular project.

So here's **strategy A, a single monitoring workspace for large units of projects**, probably an application or application part. Now the advantages are, there's a **single pane of glass** that provides visibility into the entire group of related projects. You can also compare non prod and prod environments easily. 

However the disadvantages are, **anyone with IAM permissions to access monitoring will also be able to see metrics for all environments**. Monitoring in prod is usually done by different teams. So this approach wouldn't allow that delineation.

Although the metric data and log entries remain in the individual projects, any user who has been granted the role modelling viewer, will have access to the dashboard and have access to all data by default. This means that a role assigned to one person on one project applies equally to all projects monitored by that workspace. To give people different roles per project, and better control visibility to data, consider smaller, more selective monitoring workspaces.

So here's **strategy B, prod and non prod monitoring workspaces**. So the advantages here are there's a **clear delineation between production and other environments**. It also lowers the maintenance burden of too many monitoring workspaces such as the upcoming strategy C. Logical boundaries don't have to be production or non production. This approach of small groups of projects being monitored centrally can apply to many different Google Cloud architectures. The disadvantage is though, that you have to be careful of the monitor project groupings. This approach still provides multiple project access to monitoring data.

So here's **strategy C, where every project is monitored locally in that project.** Now the advantages are, it's a clear and obvious **separation for each project**. If the project contains dev related resources, it's easy to provide access to the dev personnel.

The project resources and monitoring resources are all in the same place. It is also easy to automate since monitoring becomes a standard part of the initial project setup.

Now the **disadvantages are, if the application is larger than a single project, you are going to be looking at a small slice of a bigger picture.** And bringing that full picture into focus might be much harder to do. So there's a number of IAM security roles related to monitoring. Now the big three are viewer, editor, and admin. To create the monitoring workspace initially, a user will need the monitoring editor or admin role in the workspaces' host project.

- The monitoring **viewer** can get read-only access to the monitoring console and API.

- The monitoring **editor** has read-write access to the monitoring console and APIs. And can write monitoring data and configurations into the workspace. And the monitoring admin has full access to and control over all monitoring resources. Pass these big three roles, monitoring roles exist to provide and limit access to alert policies, dashboards, notification channels, service monitoring, and even uptime checks. Now check the documentation for more information.

- Another **critical security role is the metric writer.** Services may need permission to add metric data to the monitoring workspace. For example, take a Google Compute Engine VM running an agent that needs to stream metrics into the monitoring workspace. To allow it the right access it needs, grant the VM service account to the monitoring metric writer role in the workspace host project.


Monitoring metric writer permits writing monitoring data to the workspace, this does not permit read access to the monitoring console. Typically, this permission is used by service accounts as in this example.

Remember, monitoring workspace is only affecting control Google Cloud resources related to monitoring.

Other tools covered in this course such as logging, error reporting, and application performance management tools are strictly project based and do not rely on the configuration of monitoring workspaces or in the monitoring IAM roles.

## Dashboards

### Understanding Dashboards

Now that we've talked about the organization of monitoring workspaces, let's create and use some dashboards. **Dashboards are a way for you to view and analyze metric data that is important to you**. They give you graphical representations of key signal data, in such a way as to help you make key decisions about your Google Cloud-based resources. 

Dashboards are assembled from one or more individual charts laid out in a particular way. Here we see a portion of dashboard displaying two charts, disk IO and network traffic. One of the challenging aspects of monitoring is Google's commitment to providing a more opinionated default information. Google Cloud sees that your project contains Compute Engine VMs or a Kubernetes cluster. 

So monitoring autocrats dashboards for you that radiate the information that Google thinks is important for those two resource types. As you add more resources, Google will continue to add more default dashboards. If nothing else, these dashboards form a great monitoring foundation on which you can build, you **assemble dashboards from individual charts**. 

**A chart takes a metric that raw signal data and it brings it into windows of time such as alignment**. It does math to each aligned window to reduce it to a single value and it graphs the resulting points into some chart type. Ultimately, you get a picture that radiates some useful information. Take a look at the top right chart. There you can see a stacked bar chart displaying request count rates collected over a five-minute intervals. If you look at the other charts, you'll see they each identify the information displayed the alignment period and the math used to reduce each alignment windows into plausible values, rate and mean in these examples. 

Charts get their raw data from metrics. Google has upwards of 1,000 metrics they're pre-created. You can augment that list by using the API and creating your own where needed. Here we see an example metric from Google's documentation. This is from the Cloud Storage APIs metric, so it's related to Storage buckets. At the top left, we see the actual and the human-readable versions of the metric name. Below that is the metric descriptor Delta, INT64, 1, metric descriptors list the metric kind, value type, and unit. The available metric kinds are gauge, in which each data point is an instantaneous measurement of the value, think of the fuel gauge in your car. 

There's Delta, which reports the change in value over the time interval. So think of a car gauge which showed your changes in fuel mileage and cumulative. Now this is a value accumulated over time. This might be the total miles on your car. The value type options are Boolean, INT64, double string and distribution. The last part of the metric descriptor is the unit in which the value is returned. 

Units are only valid for the value types INT64 double or distribution. They're based on the unified code of units for measured standard, examples include bits, seconds, minutes, hours, etc. In this example, the one represents a unitary dimensionless unit, typically used when none of the basic units are appropriate. Below that gcs_buckety is the monitor resource, in this case, a Google Cloud Storage bucket. 

The description to the right tells us a bit more about the metrics, what it represents and how often it's sampled, and how it's being calculated. At the bottom are the labels, in this case response_code and method, labels can be used for group buying or filtering operations. In this case, for example we could use the API method to differentiate bucket request types.

### Creating Charts

Now that we can figure out the metrics that Google Cloud has and can read the documentation to see what each metric actually means, let's put those metrics to work in charts. A note on security IAM roles related to charts and dashboards. Now, `roles/monitoring.dashboardEditor` can be used to edit dashboard settings and `monitoring.dashboardViewer` can view dashboard settings, while `monitoring.editor` can create dashboards and add charts, `monitoring.viewer` can view charts and dashboards. 

The monitoring editor and viewer roles can do a lot more than what's stated on the slide in terms of monitoring related activities. But we're focusing on just the dashboard and chart abilities at this point. Frequently, the easiest way to start chart creation is to build an Ad Hoc chart with Google metrics explorer. Metrics explorer lets you build charts for metrics collected by your project. With it you can save charts you create to a dashboard, share charts by their URL, view the configuration for charts as JSON. Most importantly, you can use metrics explorer as a tool to explore data that you don't need to display long time on a dashboard. 

As seen in the slide, the metrics explore interface consist of two primary regions, a configuration region where you pick the metric and its options and the chart displaying the selected metric. You define a chart by specifying both what data should be displayed and how the chart should display it. 

To populate the chart, you must specify at least one pair of values. The monitored resource type or monitored resource, or just simply Resource and the metric type, also known as the metric descriptor or just metric. If you prefer, you can use Direct Filter Mode by hitting the question mark icon as seen in the diagram. When the Direct Filter Mode is enabled, defined resource type and metric option is replaced with an editable text box labeled resource type, metric and filter. 

Let's look at an example. First, you set the resource type to GCE VM Instance. We're looking at metrics related to compute engine virtual machines. Next, you pick the logging agents log entry count metric. If you check the agents metric documentation, you'll see this is a cumulative count of log entries written by the logging agents sampled every minute. You'd also see that it has a single response code label. You can see that reflected in the diagram by the error and warning entries. This example is a good start, but there's so many lines being displayed. A single chart can plot up to 300 individual lines, but it's tough to see all that detail through that much clutter. How about we filter some of them out and group others? 

We can start by reducing the amount of data return for a metric by specifying a **filter**. Filtering removes data from the chart by excluding time series that don't meet your criteria. This result is fewer lines on the chart and hopefully a better signal to know its ratio. When you click on the "Filter" field, a panel containing list of criteria by which you can filter appears. In broad strokes, you can filter by resource group, by name, by resource label, and by the metric label. In this example we will filter by zone. The zone can be compared to a direct value like `asia-east1-a`, or by using the equal tilde operator to any valid regular expression as you can see in the right-hand examples. If you'd like to see the fully supported syntax, please check the documentation. 

That's a little better. Now, instead of all the VM log counts, we've restricted our chart to just displaying those VMs in the Asia based regions. Like filtering, grouping is a way to reduce the amount of data you're manipulating. Filtering works by excluding some time series and grouping works by identifying sets of time series that all meet some criterion and then combining or aggregating the members of these sets together. 

The `Group By` option lets you group by time series, by resource and metric label, and then combines the data within those groups. This creates a single new time series for each combination of Group By values and the new time series represents all of the members of the group. To continue our agent log entry count example, let's take the VMs which had been filtered to the Asia regions and group them by zone. You can then see the results on the side. The aggregator lets you combine time series by using common math functions. The result is fewer lines on the chart displaying the metric which can improve the charts performance. Click in the aggregator field to see a list of the available aggregation functions or reducers that can be used to combine the time series. 

The available reducing functions depend on the type values that the metric captures, but they commonly include choices like mean, max or min, standard deviation, assorted percentile values, and so forth. For more information about these dependencies, see the metrics, time series, and resources documentation. We're using a slightly different example on this slide. In the first screenshot, we see CPU usage for all GKE Containers with a container name that starts with a D. Notice the aggregator is set to none. In the second example, the aggregator function is set to sum. Take a moment in time, grab all the aligned data points for that moment and add them together. See how all the lines have been combined, or aggregated. Here we have an extension of that same example. The resource is set to GKE Container, CPU usage is the chosen metric. Lastly, the CPU usages for containers and each of the two resulting groups were summed. In the advanced options for the metrics explorer, you can directly access alignment being used by the chart. A time series is a set of data points in temporal order. To align a time series is to break the data points into regular buckets of time, the alignment period. Multiple time series must be aligned before they can be combined. 

**Alignment** is a prerequisite to aggregation across time series and monitoring does it automatically by using default values. You can override these default values by using the alignment options, aligner and alignment period. The alignment period determines the length of time for subdividing the time series. For example, you can break a time series into one-minute chunks or a-one-hour chunks. The data in each period is summarized so that the single value represents that period, the default alignment period and the minimum is one minute. Although you can set the alignment interval for your data, time series might be realigned when you change the time interval displayed on a chart or change the zoom level. The aligner is a function that determines how to summarize the data in each alignment period. 

**Aligners include the sum, the mean, and so forth**. Valid aligner choices depend on the kind and types of metric data time series stores. When you have multiple time series that already represent Group By aggregations, then the secondary aggregator can do exactly that, aggregate the groups the second time. The legend template field let you customize a description for the time series on your chart. By default, these descriptions are created for you in time values of different labels in your time series. Because the system selects labels, the results may not be helpful to you. 

You can use this field to **build a template for the descriptions.** The Legend Template field accepts both plain texts and label patterns. The syntax can vary on the patterns, but the dynamic replacement sectors use the dollar sign variable notation. If you know the variables names, you can manually type them into the template field. You can also select variables for the available labels using the other filter widget in the field. This approach ensures that the variable syntax is correct. In this example, you can see the mix of static text, I'm from and the variable, `$(resource.labels.zone)`. You can see the results output in the chart legend. Click on any of the legend column headers to sort by that field. The legend columns included in the charts display is configurable. 

The View Options tab and the chart definition interface lets you specify how data is presented in the chart. One of the options is chart mode. Charts provide multiple viewing modes, though not all of them may be available for every chart. For a given chart, the possible modes are color, statistics, x-ray, and outlier. Color mode is the default and it's the display mode you most frequently encounter. Statistics mode displays common statistical measures for the data in a chart. When you select statistics mode, the chart displays a banner that shows the maximum and minimum values as well as a measure of similarity. X-ray mode displays all the graph lines with a translucent gray color. Each line is faint, and where lines overlap across, the points appear brighter. 

Overlapping lines creates bands of brightness, which indicates a normal behavior within a metrics group. Displaying a large number of lines on a single chart can obscure the interesting ones. We've addressed this with filtering and grouping, but another option might be outlier mode. In outlier mode, you can select the number of time series to show whether you want an extreme high or low values and the method by which the time series are ranked. 

Additionally, you can add a visual threshold line at a particular value. You can use the Compare to Past option to select a time range from the past and then superimpose the past series as a dotted line over the current data on a line chart. You can also rescale the charts y-values logarithmically. This rescaling is useful when values cluster tightly within a small range. In addition to the options on the View Options tab, you can also specify how the chart graphs the data. The default style is a line chart, but stacked bar, stacked area and heatmap are also available.

### Dashboard Construction

Speaking of dashboards, let's spend a moment and talk about how they're constructed.

It's easy to share charts from the Metrics Explorer. For quick one off charts, simply just share the URL. The URL contains all the selected options from the current chart, and dynamically recreates those selections whenever you visit it.

For **longer term usage, it might be easier to place the chart on a dashboard or on some external page using an iframe**. Dashboards can also add the ability to view multiple charts from a single location.

In addition to predefined dashboards, cloud monitoring lets you define custom dashboards. With custom dashboards, you have complete control over the charts that are displayed in the configuration. You can create these dashboards to the Google Cloud Console or by using the dashboard endpoint in the cloud monitoring API.

Dashboards can be created from the monitoring dashboards page by clicking Create Dashboard or directly out of the metrics explorer by clicking on Save Chart.

Once a chart is included in a dashboard, it's easy to tweak its mode, edit or view it in metrics explorer, view its logs and more. Instead of editing the properties of the charts one at a time, some features such as chart mode can be changed for all the charts in a dashboard. Dashboards can also be renamed, copied and deleted. The number of columns displayed in a chart may also be selected.

## Uptime Checks

Another monitoring component we discussed briefly in the earlier modules of this course is **uptime checks**. Error budgets quantify the organization's tolerance for errors. Instead of expecting a mythical zero error environment, the organization allows a certain number of errors to occur so that applications may be updated and properly maintained. Systems with zero errors also have zero updates, which leads to out-of-date applications, lack of innovation, and increased security risks. On the other hand, an organization that creates error budgets manages their downtime and spends it like currency. 

Think of the budget as a rainwater barrel. When problems occur, they reduce the available error budget. The budget, like the barrel, is replenished gradually over time. Application updates may incur an outage, which will in turn consume some of the error budget. If the application has sufficient error budget, the developers may take a risk and update the application. Conversely, they may decide to delay updates to applications that have little remaining error budget until the error budget replenishes. 

Developers make **changes** to gain features. 

Operations folks like **stability** because it's easier to keep things working. 

Error budgets provide wiggle room. Developers test new features and error budget provides them room for failure. Ops no longer has to choose uptime over improvements. Everyone wins. Uptime checks can be configured to test the availability of your public services from locations all around the world. As you can see in this slide, the type of uptime check can be set to HTTP, HTTPS, or TCP. The resource to be checked can be an App Engine application, a Compute Engine instance, a URL of a host, or an AWS instance or load balancer. 

**For each uptime check, you can create an alerting policy** and view the latency of each global location. Uptime checks can help us make sure externally facing services are running and that we're not burning our error budgets unnecessarily. Here's an example of an HTTP uptime check. The resource is checked every minute with a 10 second timeout. Uptime checks that do not get a response within this time out period are considered failures. So far, there's a 100 percent uptime with no outages. As discussed in the SLI, as a low part of our course, breakage is inevitable. The trick is to find the issue, fix it, and learn from the process. 

Uptime checks are one of the ways we find issues. Good uptime checks use the appropriate protocol, host and port. They check the response for specific content, so a blank page or error page won't be registered as a successful response, and they check in proportion to the criticality of the service being checked. Uptime checks are easy to create. In monitoring, navigate to uptime checks and click "Create Uptime Check." 

Give the uptime check a name and a title that is descriptive, select a check type protocol, the resource type, and the appropriate information for that resource type. A URL, for example, would need a host name and an optional page path. A number of optional advanced options are available, including logging failures, narrowing the locations in the world from where tests connections are made, and the addition of custom headers, check timeout, and authentication.

## Working with Agents

### Monitoring

Let's start with **logging and monitoring agents** for compute engine.

As we've discussed, monitoring day can originate from a number of different sources. With Google compute engine instances, since the VMs are running on Google hardware, monitoring can access some instance metrics without the monitoring agent, including CPU utilization, some disk traffic metrics, network traffic and uptime information.

But that information can be augmented by installing agents into the VM operating system. These agents are required because for security reasons, the hypervisor cannot access some of the internal metrics inside of a VM. For example, memory usage. GKE Clusters also send system metrics via an agent.

**The cloud monitoring agent is a collectd-based open source daemon** that gathers system and application metrics for virtual machine instances and sends them to monitoring.

By default, the optional but recommended monitoring agent collects disk, CPU, network and process metrics.

You can configure the monitoring agent to monitor third party applications like Apache, MySQL and Nginx.

Additional support is provided through **integration with BindPlane from Blue Medora.**

The monitoring agent supports most major operating systems from CentOS to Ubuntu to Windows.

When monitoring any of the following non virtual machine systems in Google Cloud, the monitoring agent is not required and you should not try and install it. App Engine standard, for example, has monitoring built in. **An App Engine Flex is built on top of GKE** and has the monitoring agent pre-installed and pre-configured. With standard Google Kubernetes engine nodes VMs, logging and monitoring is an option which is enabled by default.

Currently the Anthos GKE On-Prem agent collect system but not application metrics.

Cloud Run provides integrated monitoring support.

And Cloud Functions supports integrated monitoring as well.

Installing the monitoring agent is well documented on the Google site. Here, you can see examples of installing the agent on SUSE and on Debian 10. Here's another example of installing the agent in Centos 8. Please see the Google site for installing the agent into other Linux distributions or into Windows. Note, if you're using an HTTP proxy, there are extra steps for both Linux and Windows installations.

Once the agent has been successfully installed and started, execute the curl command you see at the top of this slide to get a list of the VMs access scopes from the Compute Engine Metadata Service.

In the returned list, verify that you see at least one of the scopes listed at the bottom of the slide.

**Adding credentials.**

- Normally, you won't have to perform this step but if the test on the previous slide failed, then you are missing the scope required to allow your VM to write metrics into monitoring, and we'll need to add the proper credentials.

- Start by creating a service account, granting it the monitoring metric writer role and generating a JSON key file.

- Take the downloaded file and copy it to the appropriate location depending on whether you're using Windows or Linux.

- Finally, restart the agent.


Make sure to check the Google documentation if you have any questions.

### Logging

In addition to the monitoring agent, Google also recommends installing the **logging agent into your VMs**.

By default, Google has little visibility into the logs that are created at the operating system level of your VM. If you need access to something like Windows event log or the Linux syslog, you'll need to install the Google logging agent.

Like the earlier discussed monitoring agent, the logging agent can stream logs from common third-party applications and system software to Google Cloud Logging.

It supports a number of third-party applications, including **Apache, Nginx and Jenkins** just to name a few.

Based on the open source **fluentd log data collector,** it supports standard fluentd configuration files and options.

It supports many operating systems, including CentOS, Ubuntu, Suse and Windows. Check them at documentation for details and an updated list.

When collecting logging for you, the following non-virtual machine systems in Google Cloud, the logging agent is not required and you should not try and install it.

App Engine standard and flex have logging support integrated, though there are extra logging options with flex.

With standard Google Kubernetes engine nodes, VMs, logging and monitoring is an option which is enabled by default.

Currently, the Anthos GKE On-Prem agent collect system but not application metrics.

Cloud Run includes integrated logging support.

Cloud Functions, both HTTP and background functions, includes built-in support for logging.

Installing a logging agent as well-documented on the Google site. Here you see examples of installing the agent on Linux and using PowerShell to install it in Windows.

Once the agent has been successfully installed and started, execute the curl command you see at the top of this slide to get a list of the VM's access scopes from the Computer Engine metadata service. In the returned list, verify that you see at least one of the scopes listed at the bottom of this slide.

Adding credentials, normally, you won't have to perform this step. But if the test on the previous slide failed, then you are missing the scope required to allow your VM to write metrics into monitoring.

Start by creating a service account and granting it the Logs Writer role and then generating a JSON key file.

Take the downloaded file and copy it to the appropriate location, depending on whether you are using Windows or Linux.

Finally, restart the agent. Make sure to check the Google documentation if you have questions.

### Baking an Image

In the grand scheme of things, **you don't want to have to install the logging and monitoring agents each time you create a virtual machine**. Another option would be to **bake the agents into the virtual machine images**. But this requires the image to be updated regularly so the agent stays current. 

It is also common to **use a VM startup script to install an agent**.

Some organizations are still in the habit of creating handcrafted servers. And they have no existing support or process built around the idea of image automation.

Many organizations have some version of the second option. They have a set of images that they build manually or with partial automation, perhaps for particular workloads. They don't get built or updated very often.

The goal is that organizations treat their **image creation process as a standard DevOps pipeline**. Commits to a code base trigger, build jobs, which create, test and deploy images with all requisite software and applications built in, including the logging and monitoring agents.

According to Google's image management best practices, image creation should start with a base operating system installation, preferably from a golden public base.

Periodically take the base image and have a security team harden it by removing services, changing settings, installing security components etc.

Build this hardened image every 90 days, or whatever frequency makes sense to your organization. This becomes the basis of subsequent builds.

More frequently build platform specific images, one image for web servers, one for application servers, one for databases and so on. Build these images perhaps every 30 days. During this build process you may wish to make a decision about including or excluding the logging and monitoring agents.

Finally, as frequently as you build an application, create new VM images for the new versions of that application. You might need to create new application images as often as once a day.

**HashCorp Packer** is an open source tool for creating virtual machine images. It integrates nicely with Google Cloud and can be used with cloud build to create images for Compute Engine. It really does a good job of helping to automate image builds.

## Non-VM Resources

In addition to Google Cloud virtual machines, there are a lot of Google cloud resources that support some type of monitoring. Let's take a look at a few of these.

As already mentioned in a previous lesson, Google's App Engine Standard and Flex both support monitoring. Be sure to check Google's documentation for the metrics details.

**App Engine** also supports logging by writing a standard out or standard error. For more refined logging capabilities, check out the language specific logging APIs such as Winston for Node.js. App Engine logs are viewable under the GAE Application resource. Google Kubernetes engine supports several monitoring and logging configurations. GKE logging and monitoring integration can be disabled completely. Although this will have an impact on Google's ability to support your cluster should problems arise.

System and workload monitoring and logging can also be enabled. This is currently done by default and is a Google recommended best practice.

It made at the time of this course writing, system logging and monitoring only no workload is an option. Logging data and monitoring metrics can incur billing charges. This option might lessen the cost by only capturing the system events.

An example of such a system event might look like someone created a service versus a workload event such as someone just visited the nginx container in this pod.

**Legacy monitoring and logging is still an option for clusters running Kubernetes 1.14 or earlier,** but it is not recommended. If you have older clusters with legacy monitoring and logging enabled, you should plan to update them soon.

One of the benefits of the newer version of GKE login and monitoring is access to the new Kubernetes Engine Dashboard. The dashboard provides good resource visibility into your cluster from three different perspectives.

These three **perspectives** are: 

- infrastructure, which aggregates resources by cluster then node then pod and then by container.
- Workloads which aggregates resources by cluster, the namespace, then workload then pod and then lastly by container.

- Services, which aggregates resources by cluster then namespace then service then pod and lastly by container.


Each resource name in the list is preceded by a red or green indicator. A red indicator means that the resource or a subcomponent of the resource has an open incident. A green indicator means that there are no open incidents.

Other comments display the Kubernetes object type, the number of pods, incidents, percentage of CPU and memory utilization as they relate to requested resources.

Drilling down and selecting a pod will display its details. The metrics tab includes charts for container restarts, CPU and memory utilization, networking and storage. The logs tab displays a logging rate histogram, the most recent logging messages and a link to the Google Cloud log viewer, which will auto filter to the selected pod. The details tab contains pod information including pod replica set type, namespace and the node where the pod is running.

**Prometheus** is an open source monitoring tool often used to extend Kubernetes monitoring capabilities. If your cluster has standard Kubernetes engine monitoring enabled, you can add Prometheus support by installing the Prometheus server and collector.

The Prometheus server, scrapes your metrics and then exports them in a Prometheus exposition format, through the collector, and then onto Google Cloud monitoring.

Information on installing the Prometheus server can be found on the Prometheus site. Installing the collector and accessing the metrics for monitoring is detailed in the GKE documentation on Google. In Google Cloud monitoring, the Prometheus generated metrics information will appear as `external/prometheus/* metrics.`

**Cloud Functions** are lightweight purpose built functions typically invoked in response to an event.

For example, you might upload a PDF file, to a Cloud Storage bucket. Where the new file triggers an event, which then invokes cloud function, which finally translates the PDF from English to Spanish.

Cloud Function monitoring is automatic and can provide you access to invocations, execution times, memory usage and active instances in the Cloud Console. These metrics are also available in cloud monitoring where you can set up custom alerting and dashboards to surface them.

Cloud Functions also supports simple logging by default.

Logs written to standard out or standard error will appear automatically in the Google Console.

The logging API can also be used to extend log support.

**Cloud run** is Google's Container Service. It can run in a fully managed version, which acts as a sort of App Engine for containers. Or it can run in GKE, in which case it's a managed version of the open source Knative. Cloud run is automatically integrated with cloud monitoring with no setup or configuration required.

This means that metrics of your cloud run services are captured automatically when they are running.

You can view metrics either in the cloud monitoring or in the cloud run page in the console.

Cloud monitoring provides even more charting and filtering options. The resource type differs for fully managed cloud run and cloud run for Anthos. For fully managed cloud run, the monitoring resources name cloud run revision, or cloud_run_revision. For cloud run for Anthos the monitoring resource name is cloud run on GKE revision, or Knative_revision.

Cloud run has two types of logs which it automatically sends to cloud logging. Requests logs, logs of requests sent to cloud services. And container logs, logs emitted from container instances from your own code written to standard out or standard error streams or using the logging API.

## Exposing Custom Metrics

In addition to the more than 100 metrics that Google automatically collects, you can use code to create your own. Application specific, also known as user or custom metrics, are metrics that you define and collect to capture information that built-in Cloud monitoring metrics cannot. You capture such metrics by using an API provided by a library to instrument your code. 

Then you send the metrics to Cloud monitoring. Custom metrics can be used in the same way as built-in metrics, that is, you can create charts and alerts for your custom metric data. There are two fundamental approaches to creating custom metrics for Google Cloud monitoring. 

You can use the classic Cloud monitoring API or you can use the **OpenCensus** open source monitoring and tracing library. The steps used to create a custom metric using the API are well-documented. To begin with, the data you collect for a custom metric must be associated with the descriptor for a custom metric type. Do you remember the example we saw earlier in the course of the documentation for the Cloud storage request count metric? That's what we're creating here for our custom metric.

After you have collected the information you need for creating your custom metric type, call the create method, passing information into a metric descriptor object. In this example, we are creating a gauge double metric named my underscore metric. It's a gauge metric of type double with the descriptor custom metric example. You write data points by passing a list of time series objects to create_time_series. 

Each time series is identified by the metric and resource fields in the time series object. These fields represent the metric type and the monitored resource from which the data was collected. In this example, we are using the my_metric described on the last slide and linking our metric to the specified GCE Instance. Next, we create the point by adding it to the series and adding details. Each time series object must contain a single point object. Finally, we report our metric.

What is OpenCensus? **OpenCensus** is an **open-source library to help capture, manipulate, and export traces in metrics**. It works well with both microservices and monolithic applications. Support is available in OpenCensus for a wide variety of languages, including Java, Python, Node.JS, GO, C#, Erlang, and C+. OpenCensus is low overhead and is broadly supported by various environments and back-end applications, including Cloud monitoring. 

OpenCensus metrics are expressed as **measures** and **measurements**. 

A **measure** represents a metric being recorded. It contains name, a unique identifier, description, the purpose for the measure, a string unit specifier like by or number 1 or MS, a datatype of int 64 or float 64. 

A **measurement** is a data point recorded as a measure. Views describe how measures are collected. A view represents the coupling of an aggregation applied to a measure and optionally tags.

**Views** contain name, a unique view name, description, measure, discussed on the last slide, tag keys, which can be used to group and filter metrics, and aggregation, which describes how the data is gathered. Options for this include count, distribution, sum or last value. Over the next several slides, we'll explore a quick OpenCensus example created using Node.JS. This example can be found in the Google documentation for OpenCensus. First, we need to load our core OpenCensus library and the stackdriver exporter. Next, we create a couple of variables containing the export interval and an int 64 millisecond measure named task_latency. This particular view is named task_latency_distribution, and that's what you would search for in the metrics explorer. The views reporting a distribution of latency is using the specified bucket ranges.

Now, we verify that we had the project ID and a Google Cloud service account key.

Then we enable OpenCensus to report through the exporter for stackdriver. The export interval is set to a minimum, 60 times 1000, which is one minute. With all of the set-up out of the way, we can now record measurements through our view.

The code C here generates a series of 1,000 random values and the range of 0-4. For each one, it records the measurement. Running the example code on computer engine VM yielded the pictured results. Notice the metric name,

OpenCensus/task_latency_distribution. Each bar in the heat-map represents one run of the program, and the color components of each bar represent buckets in the latency distribution.

## Strategic Logging

### Labeling

Let's start with **labeling**. 

**Labels are key-value pairs** that help organize and identify Google cloud resources that can be used to track linked resources, even if they span multiple projects. And are especially helpful when analyzing resource usage and billing. On this slide, you can see a partial list of Google resources that support labeling. Some considerations do apply when using labels.

Since labels are arbitrary key value pairs, consistency can be challenging. If one user sets a label **City:NY**, and another sets of label as **City:NYC**, GCP considers these **two different labels**, and text based searching could easily find one and completely miss the other. Consider applying labels programmatically where possible to help maintain consistency.

Choose labeled keys and values that are simple and which use standard organizational terminology and knowledge.

Labels follow the JSON format of key colon value. Label keys and values must be less than 63 characters long and can only contain lowercase letters, numbers, underscores and dashes.

### Working with the Log Viewer

Cloud logging allows you to store, search, analyze, monitor and alert on log data and events from Google Cloud. Cloud logging is a fully managed service that performs at scale and can ingest application and system log data from thousands of VMS. Even better, you can analyze all that logged data in real time.

To start using Google Cloud logging, start with the **logs viewer**. There are two querying interfaces in the logs viewer.

The basic querying interface that you can see on this slide lets you select logs from menus and has a simple search capability.

The advanced query interface we will examine in a few minutes, lets you view log entries from multiple logs and has a more sophisticated search capability.

In the screenshot on this slide you see the logs viewer layout, with the basic query interface active.

In this example, we see a list of log entries from an App Engine application.

The UI allows log entries to display in different time zones for globally distributed teams.

Newly added features include the ability to view data histograms to see if an event distributions over time. And to view aggregated log data, such as resource type, resource labels, log name and severity using the new logs field explorer.

So, let's take a quick tour.

The basic query interface has the following major components indicated by red numbers in the screenshot, many of which are shared with the advanced querying interface. 1, the Windows tablets you stay on logs, the logs viewer page, or choose between other logging features, metrics, exports, and logs ingestion. 2, the search query box in the basic query interface lets you log queries by label or text search. The basic query is shown and the drop down arrow at the far right lets you switch the advanced query interface or get a link to your query.

Log queries are labeled as filters in this user interface, since they let you select a particular set of logs.

In the basic selector menu 3, you can choose resources, logs and severity levels to display.

Resources are the resources available in your current project. Logs are the log types available for the current resources in your project.

**Log severity is the log severity level.**

4, the time range selector for drop down menus lets you query for specific dates and times in your logs.

5, the streaming selector at the top of the page controls whether new log entries are displayed as they arrive.

6, the log entry table contains log entries available according to your current queries and custom fields.

7, the expander arrow in front of each log entry lets you look at the full contents of that entry. For more information, you can see Expand Log Entries on the documentation pages.

8, the view options menu at the far right has additional display options. 9, the download logs menu also at the far right lets you download a set of log entries. 10, the more option displayed with each log entry lets you place a pin in the log entry, show the log entry and its resource context and copy a URL for the log entry to the clipboard.

The basic filters are good for fundamental field searches such as searching for entries that contain foo in any field. You can filter for text foo, or simply foo.

To locate entries that contain foo or bar, then filter for text foo text bar or foo bar.

To find entries that contain foo and bar, use quotes, as in filter for text foo bar, quote unquote, or quote, foo bar quote.

Basic features won't support wildcard characters like asterisk in foo asterisk.

Won't support searching the timestamp field like 2020-02-05. Won't support the use of range operators as in 200..299.

The log entry table displays a summary line for each log entry by default.

The log entry summary line might contain highlighted fields. For example, custom fields are highlighted.

The fields included in the summary line are selected as subsets of the log entry fields. Certain fields are shown by default if they meet one or more of the following criteria.

The log entry has a well known type, such as an App Engine request log.

The log entry contains the HTTP request field,

the log entry has a payload containing a field named message.

To see the full details for one log entry, click the expander arrow at the front of the summary line. To see the full details in a structured view for all the log entries available with your current query, click the View Options menu at the far right and then select Expand All.

When you expand a summary line for a log entry, the logs viewer displays a structured JSON representation.

Log entries are based on Google's log entries data type. They contain data like log name, severity, resource type, and various payload fields. Note that JSON payload and text payload are mutually exclusive. You can only use one or the other of these fields but not both.

You can click the value of a specific field in the expanded log entry view, and then either show or hide all log entries with that same value. Doing so will also switch the logs viewer to advanced mode.

The logs viewer advanced mode gives you the ability to create more advanced queries. The syntax of the queries is similar to the basic view, but it's not exactly the same.

The next several slides are included for reference. Advanced query support multiple comparison operators as seen here.

Advance query support the AND, OR and NOT Boolean expressions for joining queries.

An advanced queries can support grouping, optional sections, and repeat values.

Some tips on finding log entries quickly, search for specific values of index fields like the log entries name, resource type, and resource labels.

As seen in this example, be specific about which logs you are researching by referring to it or them by name.

Limit the time range you are searching for to less than the amount of log data being queried.

Be wary of global searches, they add a lot of overhead. But when all else fails, they're a great way of scanning everything.

### Using Log-Based Metrics

Now that we've seen how the logs viewer works, let's talk about generating monitoring metrics from logging data. Let's review some of the key IAM roles that relate to logging and monitoring first. On the logging side, we have logs configuration writers who can list, create, get, update, and delete logs-based metrics. We also have logs viewers who can view existing metrics. On the monitoring site, monitoring viewers can read the time series in logs-based metrics. Finally, logging admins, editors, and owners are all broad level roles that can create logs-based metrics. 

**Logs-based metrics** are cloud monitoring metrics that are based on the **content** of log entries. Resources generate logging events that are streamed into Google Cloud logging. Log-based metrics apply a **filter** to locate particular entries. For example, the metrics might record the **number of log entries containing particular messages** or that were generated by a particular resource. Once created, you can use logs-based metrics and Cloud monitoring charts and alerting policies. Once again, please don't reinvent the wheel. Before creating your own custom logs-based metrics, take a look at Google's curated list of pre-existing metrics. 

There are over 1,000, and what you're looking for might already be there. Custom metrics should generally only be used if the metric you need is not readily available or if you need to create an application metric. Using custom metrics may also increase your monitoring logging costs and this should be taken into consideration. Before we create a simple logs-based metric, let's generate some logging entries. 

Here we see a basic NodeJS app built with the simple and lightweight express webserver, which we will run as a managed container on Google's Cloud Run service. The cloud watches for requests to come into the server on the slash score path. When a slash score request arrives, the code generates a random 0-99 score and then it creates a log entry. Earlier code, not shown on this slide, created a unique identifier for the container serving this request in containerID, a random value called funFactor. 

It also loaded the Winston NodeJS logging library, which was integrated with Google Cloud logging. The log entry contains the text slash score called and a JSON object containing the random score, the containerID, and the funFactor. Lastly, a basic message also containing the score is sent back to the browser. Note that when using Winston logger for Google Cloud, by default, the entries are made into the slash projects, slash Project ID, slash logs, slash Winston log, which can be found in the global category. 

Now, imagine we've generated some load on our Cloud run sample application and we'd now like to use the log events to generate a logs-based metric. 

There are two fundamental logs-based metric types. 

**System logs-based metrics**, which are predefined by Google and our standard part of logs-based metrics is one. 

Then there is **user-defined logs-based metrics** which are created by the user on a particular project. These count the number of log entries that match a given query or keep track of particular values within the matching log entries. The ladder is what we're creating now. You'll notice the Create Metric button at the top of the interface. The basic flow for creating logs-based metrics goes something like this. First, you start by finding the log with the requisite data, then you filter it to the required entries. After this, you'll pick your metric type counter or distribution. If you pick distribution, you'll then set configurations. Finally, you can add labels as needed.

You can see here that the logs viewer advanced view is being used. The resource.type has been set to global, since that's where the Winston log will appear. In the list of entries, we'd located one of these slash score called entries, and now we can filter to select those by clicking on slash score called and selecting show matching entries. 

Logs-based metrics can be **one of two metric types**, as already mentioned, **counter** or **distribution**. 

All **pre-defined system log-based metrics are of the counter type**. A user-defined metrics can be either counter or distribution types. Counter metrics, as the name implies, counts the number of log entries matching an advanced logs query. So if we simply want to know how many of our slash score called entries were generated, we could create a counter. Distribution metrics, on the other hand, records a statistical distribution of the extracted log values in histogram buckets. The extracted values are not recorded individually, but their distribution across the configured buckets are recorded along with the count, mean and sum of squared deviations of the values. 

**Distribution metrics include a histogram that counts the number of values** that fall within a specified range in the buckets. There are three different ways to specify the boundaries between histogram buckets. 

You can use a **linear distribution**. Every bucket has the same width. 

You can use **exponential**; bucket widths increase exponentially for higher values. Explicit, you can list all of the boundaries for the buckets in the bounds array; bucket i has these boundaries. Whichever methodology you use, be aware that a distribution only supports up to 200 buckets. Like many cloud resources, labels can be applied to logs-based metrics. Their prime use is to help with the group-by, and filtering, and cloud monitoring. Labels allow logs-based metrics to contain multiple time series, one for each label value. 

All logs-based metrics come with some default labels and you can create additional user-defined labels in both counter type and distribution type metrics by specifying extractor expressions. 

An **extractor expression** tells Cloud Logging how to extract the labels value from log entries. You can specify the labels value as either of the following. One, the entire contents of a name field in the log entry object, or two, a part of a named field that matches a regular expression. 

You can extract labels from the **LogEntry built-in fields**, such as HTTP requests dot status, or from one of the payload fields: **text payload, JSON payload, or proto payload**. Labeled with care, a metric can support up to 10 user-defined labels. Once created, a metric cannot be removed. Also, each logs-based metric is limited to about 30,000 active time series. Each label can grow the time series count significantly. For example, if your log entries come from 100 resources, for example, VM instances, and you define a label with 20 possible values, then you can have up to 2,000 times series for that one metric.

User-defined labels can be created when creating a logs-based metric. The label form requires the following; 

- **name**, the identifier which will be used when selecting the label in monitoring. 
- **Description**, describe the label and try to be as specific as possible. 
- **Label type**. Here you can choose between string, boolean, or integer. 
- **Field name.** Enter the name of the log entry field that contains the logs, the labels value. This field supports autocomplete. 
- **Extraction regular expression**. If your labels value consists of the fields entire contents, then you can leave this field empty, otherwise, specify a regular expression that extracts the label value from the field value. 

Once created, the label and its time series will be available through the metrics explorer and other monitoring services. In this example, you can see the metric is set to log_entry_count. At the bottom, you can filter by the log name or by the severity labels.

## Exporting and Analyzing Logs

Now that we understand the core elements involved in strategic logging and error reporting, let's look at how logs can be exported for long-term storage and analysis. What we call Google Cloud logging is actually a collection of components. Users need to get logging events into the logging system, so facing the outside is a centralized logging API. To give users a choice about where their logs go, Google created the Log Router. 

**Log Router** is optimized for **processing** streaming data, reliably **buffering** it, and **sending** it to any combination of **BigQuery, GCS, Pub/Sub**, and of course, log management. If users are already happily using Splunk or some other external system or code base, routing through Pub/Sub can easily make logging events available. 

Log Router can also sync to **BigQuery** or **Cloud Storage** for long-term storage, with BigQuery having the added benefit of being able to run massively scalable queries over those logs. Finally, if users want to see their logs for operational or troubleshooting purposes, we have the Cloud-based storage and interface abilities native to Google Cloud, and covered earlier in this module. Inclusion and exclusion filters can control exactly which logging entries end up at a particular destination, and which are ignored completely. 

To track your project's log volume, go to the resource usage page and Cloud logging console. The top of the page displays a summary of statistics for logs that your project is receiving, including last month's ingested log volume, which is the amount of logs your project received in the last calendar month. 

This month's ingested log volume; the amount of logs your project has received since the first date of the current month. 

Excluded log volume; the amount of logs that you have excluded from your projects since the first date of the current month. This number is not included in this month's ingested log volume, excluding logs is covered on the next slide. 

Then last, projected ingestion log volume; the estimated amount of logs your project will receive by the end of the current month based on current usage. Below that, you have two tabs showing the ingestions and exclusions by type. One note, the **log volumes don't include admin activity audit logs or all system event audit logs.** These logs are free and cannot be excluded or disabled for security purposes. To create logging exclusions, start by clicking the exclusions tab. On the logs ingestion exclusions tab, click the create exclusion button. You'll be presented with a split UI. On the left is the logs viewer, use it to correctly identify the logs you want to exclude. Take care here because excluded log events will be lost forever. Once the entries are identified and a query built, give the exclusion a name and a description, and then decide the percentage of log entries to exclude. It might be helpful to leave some representative events, depending on what type of exclusion you're using. Create the exclusion, and it will go into effect immediately. 

**Log exports** can be used to forward copies of some or all of your log entries outside of Cloud logging. Common reasons for exporting include: storing logs for extended periods, using big data analysis tools on your logs, and streaming your logs to other applications, other repositories, or to third parties. 

**Log sinks** currently support three main targets: **BigQuery, Cloud storage, and messaging via Cloud Pub/Sub**. 

**BigQuery** is a common log sink as it allows both long-term storage and the ability to implement powerful analytics using SQL queries. 

**Cloud Storage** supports inexpensive, unlimited retention periods, life cycle control, and locks. 

Lastly, **Pub/Sub** messages allow logging events to be streamed to any system or code that can support http based messages. This option tends to be used to integrate third-party tools or to trigger automated actions. 

The process for creating log sinks mimics that of creating log exclusions, it involves writing a query that selects the log entries you want to export in the logs viewer. Then choosing a destination which can be Cloud storage, BigQuery, or Pub/Sub. The query and destination are held in an object called a sink. Sinks can be created in Google Cloud projects, organizations, folders, and billing accounts. Over the next several slides we will investigate some possible log exporting processing options. Here for example, we are exporting through Pub/Sub, then to Dataflow, and finally to BigQuery. Dataflow is an excellent option if you are looking for real-time log processing at scale. In this example, the Dataflow job could react to real-time issues while streaming the logs into BigQuery for long-term analysis. We've already discussed one variation on this pattern. If the real-time Dataflow analysis wasn't needed, we could stream directly from logging into BigQuery.

**Sink pipelines** targeting Cloud Storage tend to work best when your needs are in line with the Cloud Storage's strengths, like **long-term retention, reduced storage costs, and configurable object life cycles**. Cloud storage features include automated storage class changes, auto-delete, and guaranteed retention.

Here we have an example of an organization that wants to integrate the logging data from Google Cloud back into an on-prem Splunk instance. Pub/Sub is one of the options available for exporting to Splunk or to other third party system information and event management software packages. In our final example, we have Google Cloud resources creating logging data in an application project linked to Account 1.

We have a separate secure logging project designed to provide access to Account 2, but only to some of the logging data.

In the appropriate VMs in the application project, we've customized the fluentd configuration file to send specific log messages in a specific format. We've then sent those particular messages to the secure login project using a log sink. One of the most common needs regarding logs is centralized logging data in a single location for auditing, retention, and non-repudiation purposes. There are three available Google Cloud logging aggregation levels. The first is a project level log sink, which we've discussed already. This option exports all of the logs for a specific project, and a log filter can be specified in the sink definition to include or exclude certain log types. A folder level log sink aggregates the logs on the folder level and can include logs from child resources such as subfolders or Projects.

For a global view, an organization level log sink can aggregate logs on the organization level and can also include logs from child resources, again, such as subfolders and Projects. An aggregated sink can export log entries from all of the Projects, folders, and billing accounts of a Google Cloud organization. For instance, you might aggregate and export audit log entries from all of an organization's projects to a central location. The destination for log sinks has to be created before you use the sink. Once again, the supported destinations are a Cloud Storage bucket, a Pub subtopic, or a BigQuery table. To create an aggregated sinking Google Cloud folders, billing accounts, or organizations, you can use either the Cloud Logging API or the gcloud command line tool.

Here we see an example sink created using gcloud. You must supply the SINK_NAME, the sink destination BUCKET_NAME, which is why that must be created prior, the logs query, and the idea of the folder billing account organization. Note here, that we are filtering for the log name activity.

Other valid options besides folders include organizations and billing accounts. You would need the Logs Configuration Writer Cloud IAM role for the parent to create the sink. We've mentioned several times that a common export destination for logs is BigQuery. BigQuery has many features that can be of use when processing/exporting logged data. It support stream loading logs to support easy access to real time insights while serving as a good foundation for making these insights easily accessible. BigQuery can store data both securely and inexpensively. It can form an excellent foundation for AI system training data and simplify data operations through it's easy to use and see 2011 compliant SQL interface. BigQuery results can also be visualized using tools such as Datastudio and Looker.

BigQuery table's schemas for export of logs are based on the structure of the log entry type and the contents of the log payloads. Cloud Logging also applies some special rules to shorten BigQuery schema field names for audit logs. You can view the table's schema by selecting a table with exported log entries in the BigQuery web UI, as seen on this slide. There are a few naming conventions that apply to log entry fields. For log entry fields that are part of the log entry type, the corresponding BigQuery field names are precisely the same as the log entry fields. For any user supplied fields, the letter cases normalize to the lowercase, but the naming is otherwise preserved. For field unstructured payloads, as long as the app type specifier is not present, the letter case is normalized to the lowercase, but naming is otherwise preserved. For information on structured payloads where the at type specifier is present, see the payloads fields with at type documentation. You can see some examples on the current slide. Here's a sample query over the Compute Engine logs. It retrieves log entries for multiple log types over multiple days. The query searches the last three days, for example, today, minus two of the sys log and Apache access logs. The query retrieves results for the single compute instance id scene and the where clause. In this BigQuery example, we are looking for unsuccessful app engine requests from the last month. Notice how the from clause is constructing the table data range. The status not equal to 200 is examining the HTTP status for anything that isn't a 200. That is to say anything that isn't a successful response.

## Error Reporting

Now that we've spent some time learning about core logging, let's see how Google moves past simply reporting error logs by taking a look at error reporting. 

**Error Reporting counts, analyses, and aggregates the crashes** that occur when you're running Cloud services. It provides a centralized error management interface and displays the results with sorting and filtering capabilities. A dedicated view shows the error details, time chart occurrences, affected user count, first and last seen dates, and a cleaned exception stack trace. You can also opt to **receive e-mail and mobile alerts** for new errors. 

Support is available for many popular languages including Python, Java, Node.js, GO,.NET, PHP, and Ruby. Use Google's Cloud libraries or Rest API or send errors with Cloud logging. First, to report errors, your code will need the Error Reporting Writer IAM role. Error reporting is available in many Google compute environments, App Engine, Cloud Functions and Cloud Run are configured to use error reporting automatically. 

In Google's Kubernetes and Compute Engine, you can either use error reporting API to report errors, or you can use logging. A side note, a log message for any language or environment using ReportedErrorEvent formatting will also be reported to Error Reporting automatically. Let's look at an error catching example written in Node.js and run on Google's Cloud Run. The full source for this example can be found on GitHub, and you'll also see it again in the upcoming lab. 

Since **Cloud Run is an environment that has pre-integrated support for error reporting**, we don't have to do anything special to get error reporting working. In languages like Node, exceptions are used to wrap error messages. Exceptions are language way of saying, "Hey, something bad happened and here's some details about it." Exceptions can be caught and handled by code, or they can be bubbled out to the environment. In this case, the doesNotExist function literally isn't defined by the code.

As a result, calling it will generate an unhandled exception, which in turn will generate a report in error reporting. To manually log errors to error reporting in Node, the easiest way is to import the error reporting library. When creating the new error reporting object, the report mode configuration option is used to specify when errors are reported to the error reporting console. It can have one of three values. Production, which is the default, only reports errors if the node ENV, environmental variable, is set to production. 'Always', always reports errors regardless of the value of node_ENV. 'Never', never reports errors regardless of the value of node_ENV.

Now let's actually handle an error. This example method also calls `doesNotExist`, but it handles the error itself by catching it, instead of just letting it bubble out to the environment. The catch first logs the error through the Winston logging library integration for Cloud logging. That generates an entry in the `projects/yourproject_ID/logs/WinstonLogfile`, but it does nothing in error reporting. That will manually report the error to error reporting using errors.report.

To see your errors, open the error reporting page in Google Cloud console. By default, Error Reporting will show you a list of recently occurring open and acknowledged errors in order of frequency. Errors are grouped and de-duplicated by analyzing their stack traces. Error Reporting recognizes the common frameworks used for your language and groups errors accordingly. Here, if you look at the top error, you'll see that three errors have been generated by calls/error. The error states that, doesNotExist is not defined, and the error came out of one of the hello logging Cloud Run services. Error Reporting samples up to 100 errors per hour. When this limit is reached, the displayed counts are estimated. If too many events are received for the whole day, Error Reporting can sample up to 100 errors per hour and continue to extrapolate the counts.

You may **filter, sort, and review additional details** about errors as well as restrict errors that appear on the list to a specific time range.

Selecting an error entry will allow you to drill down into the error details page. On this page, you can examine information about the error group, including the history of a specific error, specific error instances, and diagnostic information contained in samples for the error. Sample errors are found in the recent samples panel. Each sample represents one occurrence of the error, and includes a parsed stack trace. To view the log entry associated with the sample error, click view logs from any entry in the recent samples panel. This takes you to the logs viewer in Cloud logging console

# Monitoring Network Security and Audit Logs

## Monitoring Network Security

### VPC Flow Logs

Let's start with monitoring the network. **VPC flow logs record a sample about one out of every 10 packets of network flows sent from and received by the VM instances**, including Kubernetes Engine notes. These logs can be used for network monitoring, traffic analysis, forensics, real-time security analysis, and expense optimization. VPC flow logs is part of Andromeda, the software that powers VPC networks. 

VPC flow logs introduce **no delay or performance penalty** when enabled. You can enable or disable VPC flow logs per VPC subnet. Once enabled for subnet, VPC flow logs collect data from all VM instances in that subnet. Each log entry contains a record of different fields. For example, this table illustrates the IP connection information that is recorded. This consists of the **source IP address and port, the destination IP address and port, and the protocol number.** This set is commonly referred to as a **5-tuple**. Other fields include the start and end time of the first and last observed packet, the bytes and packets sent, instance details including network tags, VPC details, and geographic details. For more information on all the data recorded by VPC flow logs, please see the VPC flow logs documentation.

The Google Cloud logs viewer can be used to access the VPC flow logs. In logs view, select the GCE subnetwork resource and in the logs filter, change all logs to VPC flows. **Exploring VPC flow logs to BigQuery** allows you to analyze your network traffic with SQL to understand **traffic growth patterns and network usage** better. For example, in this screenshot, we queried logs to identify the top IP addresses that have exchanged traffic with the web server. 

Depending on where these IP addresses are and who they belong to, we could relocate part of the infrastructure to reduce latency, or we could denialist some of these IP addresses if we don't want them to access our web server. For more sophisticated visualizations, connect your BigQuery tables to Data Studio and transform the raw data into the metrics and dimensions needed to create end user friendly reports and dashboards.

### Firewall Rules Logging

Another essential part of knowing what's happening at the VPC network level is knowing what the firewall rules are doing. 

VPC firewall rules let you allow or deny connections to or from your virtual machine instances based on a configuration that you specify. **Enabled VPC firewall rules are always enforced**, protecting your instances regardless of their configuration and operating system, even if they have not yet started up. 

**Firewall Rules Logging allows you to audit, verify, and analyze the effects of your firewall rules**. It can answer questions like, did my firewall rule cause that application outage? 

How many connections match the rule I just created? 

Are my firewall rules stopping or allowing the correct traffic? 

By default, Firewall Rule Logging is disabled. You can enable it on a per rule basis. In the slide screenshot, the user is editing the firewall rule named enable-rdp. Selecting the radio button will enable firewall rules. Caution, Firewall Rule Logging can generate a lot of data which may have a cost impact. Firewall Rule Logging can also be enabled on existing firewall rules using the command line interface. See these two examples on the slide, in both, name would be the name of your firewall rule. Like all Google Cloud logs, use the logs viewer to view logs in real-time or to configure exports. BigQuery is frequently used to simplify firewall rule log analysis.

To filter for firewall logs, set the resource to GCE subnet and the log file to firewall. A lot of users are familiar with the classic segmentation of gateway-centric firewalls. In this example, you can see a private network, possibly one at your office or home. At the network boundary where the private network meets the outside network, sits a firewall. A segmentation firewall is designed to segment and secure a protected network from an outside insecure network. **Google Cloud VPC firewalls are micro-segmentation firewalls**. 

These function more like a bunch of micro firewalls, **each sitting on the neck of every VM connected to your virtual private Cloud**. The micro firewalls can then grant or deny any configured incoming or outgoing traffic. Now, imagine we have an issue. We have two different web servers and after some configuration changes by a particular DevOps team, the web service can no longer access the application server they both share. How can we tell if this is a firewall related issue? Let's see. If the connectivity issue is related to a firewall, then we know there are two major possibilities. 

One, there's a **firewall rule that's actively blocking** the incoming connections from the web servers or two, since network traffic is blocked by default in most networks, there could be a **firewall rule that isn't allowing the traffic from the web servers as it should**. You will notice these two possibilities are basically two sides of the same coin. 

Logging all deny connections could generate a lot of data that would take time and effort to go through. So instead of starting with option 1, let's start with option 2, create a temporary low-profile rules specifically designed to allow the web server traffic through to the appserver. Enable logging on it so you can examine the entries. Suddenly the traffic is getting through, so now you know it's a firewall related issue. Now, examine the log entries, find the existing rule that's supposed to be allowing traffic and see what you can find out. Hey, look at that, the rule that's supposed to be allowing traffic is based on a network tag named web server. The web server machines are actually using the network tag web-server. There it is, that's your problem.



### Cloud NAT Logs

Another piece of the network telemetry features in Google Cloud is Cloud NAT Logs. 

Cloud NAT, Network Address Translation allows Google Cloud Virtual Machine instances without a external IP addresses in private Google Kubernetes Engine clusters to send outbound packets to the Internet and receive any corresponding established inbound response packets. 

**Cloud NAT is a distributed software-defined, fully-managed service grounded in the Andromeda software** that powers your VPC network. It provides Source Network Address Translation, SNAT for VMs without external IP addresses, as well as Destination Network Address Translation, DNAT for established inbound response packets. Cloud NAT benefits include, security. You can reduce the need for individual VMs to have external IP addresses, lessening the surface area for attack. You can also confidently share a set of common external source IP addresses with a destination party. 

Availability. Cloud NAT is a distributed software defined, managed Google Cloud Service. It **doesn't depend on any VMs in your project** or in a single physical gateway device. Scalability. Cloud NAT can be configured to automatically scale the number of NAT IP addresses it uses, and it supports VMs that belong to managed instance groups, including those with auto-scaling enabled. 

Lastly, performance. Cloud NAT **does not reduce the network bandwidth per VM**. Cloud NAT works directly with Google's Andromeda software defined networking. Cloud NAT logging allows you to log NAT, TCP, and UDP connections and errors. When Cloud NAT logging is enabled, a log entry can be generated when a network connection using NAT is created and/or when an egress packet is dropped because no port was available for NAT. You can opt out to log both kinds of events or just one. Logs contain TCP and UDP traffic only, and the log rate threshold will max out at 50-100 log events per vCPU before log filtering. Cloud NAT logging may be enabled when a new Cloud NAT gateway is first created, or by editing an existing gateway settings.

To view the collected logs in the logs viewer, filter to the Cloud NAT gateway resource and optionally, restrict to a particular region or gateway. The full query will look something like resource.type equals NAT gateway. Log name equals projects/ProjectID/log/ComputeGoogleAPIs.com/NAT flows.

### Packet Mirroring

Another way to monitor the network traffic flowing in and out of your compute engine virtual machines is to use packet mirroring. Packet mirroring clones the traffic of specific instances in your virtual private cloud network and forwards it for examination. Packet mirroring captures all ingress and egress, traffic and packet data such as payloads and headers.

This **mirroring happens on the virtual machine instances**, not on the network. Consequently, packet mirroring consumes additional bandwidth on the hosts.

Packet mirroring is useful when you need to monitor and analyze your security status. It exports all traffic, not just the traffic, between sampling periods. You can then use security software that analyzes mirror traffic to detect threats or anomalies.

Additionally, you can inspect the full traffic flow to detect application performance issues and provide network forensics for PC compliance and other regulatory use cases. Obviously, this can generate rate a lot of data, so the recommended target is a load balanced, compute, engine managed instance group or the equivalent. Packet mirroring exports monitoring data about mirror traffic to cloud monitoring. You can use monitoring metrics to check whether traffic from a VM instance is being mirrored as intended. For example, you can view the mirror packet or bite count for particular instance. You can view the monitoring metrics of mirror VM instances or instances that are part of the collector destination and, for example, internal load balancer.

For mirrored VM instances, packet mirroring provides metrics specific to mirror packets such as mirroring mirrored packets count, mirroring mirrored bites count and mirroring dropped packets count. Monitoring can also spot where packet mirroring is being used unnecessarily or unexpectedly. Keep in mind that, as noted, mirroring generates a lot of data that requires storage and processing. But also note that it slows the network throughput of the virtual machines being monitored and may accidentally expose sensitive data.

### Network Intelligence Center

The section is a bit of a detour. Well, let's go ahead and at least mention the Network Intelligence Center and how it helps with network analysis.

**Google's Network Intelligence Center is all about giving you centralized monitoring and visibility** into your network. Reducing troubleshooting time and effort and increasing network security all while improving the overall user experience. Currently, it offers four modules, network topology, connectivity testing, a performance dashboard and firewall insights.

Network topology is a visualization tool for viewing the topology of your VPC networks and the metrics that are associated with their Google Cloud resources. Connectivity tests enabled you to evaluate connectivity to and from Google Cloud resources in your virtual private cloud by performing a static analysis of your resource configurations. Performance dashboard gives you a visibility into the performance of your VPC network. It provides packet loss and latency that is round trip time metrics between your zones where you have the VMs. Firewall insights provides visibility into firewall usage and detects firewall configuration issues. 

Network topology visualizes your Google Cloud network as a graph. You can use the graph to explore your existing configurations and quickly troubleshoot networking issues. You can select network entities filter, see lines of communication with the band with information, expanded collapse hierarchies, select time boundaries and details for the items selected. Network Intelligence Center connectivity tests helped to quickly diagnosed connectivity issues and prevent outages. 

These tests enable you to self-diagnose connectivity issues within GCP or between GCP and an external IP address, which could be on-prem or, in another cloud. Helping to isolate whether the issue is actually in GCP or not. Run tests to help verify the impact of configuration changes and ensure that network intent captured by these tests is not violated proactively preventing network outages. These tests also help assure network security and compliance. Performance dashboard gives you visibility into the performance of your VPC. The packet loss tab shows the results of active probing between your VMs in a given VPC. To get this data, it runs workers on the physical host that how's your VMs. These workers insert and receive pro packets that are run on the same network as your traffic revealing issues on that network. 

Because the net workers run on the physical host and not on your VM, these workers do not consume VM resources, and the traffic is not visible on your VMs. Packet loss is aggregated for all zone pairs. The latency tag aggregates latency information based on a sample of your actual TCP VM traffic. Using a method similar to the one used in VPC Flow Logs. The latency is calculated as the time that elapses between sending a TCP sequence number and receiving a corresponding acknowledgement that contains the network's RTT and TCP stack related delay.

The latency metric is only available of TCP traffic is around 1,000 packets per minute or higher.

Firewall Insights enable you to better understand and safely optimize your firewall configurations by analyzing firewall rules, logs and providing reports on firewall usage and the impact of various firewall rules on your VPC. For each firewall rule with logging enabled, you can see how many times the firewall rule has blocked or allowed connections.

The last time a particular firewall rule was applied to allow or deny traffic. A list of firewall rules that haven't been used in the last six weeks and are therefore are candidates for reconfiguration or removal. And lastly, shadowed firewall rules. A shadowed rule is a firewall rule that has all of its relevant attributes, such as IP address range in ports overlapped by attributes from one or more other firewall rules with higher or equal priority. A firewall doesn't evaluate a shadowed rule because of the overlap and because the shadowed rule has a lower priority than its shadowing rules.

## Monitoring Audit Logs

### Audit Logs

In terms of sheer volume of useful information, probably the most important group of logs in Google Cloud are the audit logs. Cloud audit logs help answer the question, **who did what, when, and where?**

It maintains three audit logs for each Google Cloud project folder and organization. These are 

- Admin Activity, 
- Data Access, 
- System Event. 

All Google Cloud Services will eventually provide audit logs. For now, see the Google services with audit logs documentation for coverage details. Admin Activity audit logs contain login trees for API calls or other administrative actions that modify the configuration or metadata of your resources. For example, these logs record when users create VM instances or change Cloud Identity and Access Management permissions. They are always on and are retained for 400 days and are available at no charge. To view these logs, you must have the Cloud IAM role, logging logs viewer or project viewer. 

**Day access audit logs** contain API calls that read the configuration or metadata resources, as well as user-driven API calls that create, modify, or read user provided resource data. 

**Data Access audit logs** do not record the data-access operations on resources that are publicly shared or that can be accessed without logging into Google Cloud. **They are disabled by default except for BigQuery**. When enabled, the default retention is **30 days**. To view these logs, you must have the Cloud IAM roles, logging, private logs viewer or project owner. 

**System Event audit logs** contain log entries for Google Cloud administrative actions that modify the configuration of your resources. System Event audit logs are generated by Google systems. They are not driven by direct user action. They are always enabled, always free and are retained for 400 days. To view these logs, you must have the Cloud IAM, logging logs viewer role or project viewer role. Here's an easy but not foolproof trick to try. Put the logs viewer in Advanced Mode and simply type audit log, no space, in the filter box. That will find all audit log entries of all types. But be aware that it might also pick up non audit log entries as well. 

When looking for a particular log, a better way would be to set the resource type to the Google Cloud Resource type who's audit logs you want to see. Then you can query, logname equals log file name, where the name is one of the following. In the example, project ID could actually be the Project ID or folder ID, or organization ID. Examples are project slash project ID, slash logs, slash Cloud audit, slash Google APIs.com, slash activity. Here's an example where the resource is set to App Engine. 

The log names are then set to the Data Access and Admin Activity logs for a single project. You see the results below the query, whether it's hardware support engineer or a rep working on a ticket, having dedicated experts managed parts of the infrastructure is a key benefit of operating in Google Cloud. Access transparency logs help by providing logs of accesses to your data by human Googlers as opposed to automated systems. Enterprises with appropriate support packages can enable the logs and receive the log events in near real time, surface through APIs, Cloud Logging, and Security Command Center.

### Data Access Logging

Let's continue by taking a look at the Data Access logs. 

**Data access logs** can be enabled at the organization, folder, project, or resource level. The added logging does add to the cost, currently $0.50 per gigabyte for ingestion. Data access logs are disabled by default for everything but BigQuery. You can enable and configure data access logs at the organization, folder, project, or service level. You can control what kinds of information are kept in the audit logs. 

There are three types of data access audit logs. 

- Admin read, 
- records operations that read metadata or configuration information. 

For example, you may have looked at the configurations for your bucket. Data read, records operations that read user provided data. For example, you listed files and then downloaded one from Google Cloud Storage. Data write, records operations that write user provided data. For example, you created a new Google Cloud Storage file. You can exempt specific users or groups from having their data accesses recorded. For example, you might decide to exempt your internal testing teams accounts from having their Google Cloud Debugger Operations recorded. Set a configuration for all new and existing Google Cloud services in your project, folder, or organization to inherit ensuring that Data Access audit logs are captured. You can also use G-Cloud or the API to enable Data Access logging. If you're using G-Cloud, the easiest way is to get the current IAM policies as seen in the bullet and write them to a file. Then you can edit this file to add or edit the audit log configs. You can also add the log details per service, like this example is enabling logging for Cloud Run or even enable logging on all services. Then as seen in the bullet, you would set that as the new IAM policy.

### Understanding Audit Logs

Now that we can enable the logs we need, let's examine the logging entries themselves. Every audit log entry in Cloud Logging is an object of type log entry. What distinguishes an audit log entry from other log entries is the protoPayload field, which contains an audit log object that stores the audit logging data. Here, note the logged name, this tells us that we're looking at an example from the Data Access log. 

Now note the **audit log type in the protoPayload**.

Google has a standard list of official service names. You can use this list as a handy reference.

In this slide, you can tell we're looking at a query that was run in BigQuery. If you expanded the service data field, you can actually see the query itself, so when someone at your organization runs that unexpected $40,000 query, you can figure out who and what the query was. Then you can go learn more about the price controls for BigQuery.

### Best Practices for Audit Logs

Now, let's go over a few best practices before we wrap this module up. Plan, plan, plan. Like anything in the Cloud, start by planning first. Spend time and create a solid plan for data access logs. Think about your resources by organization, folder, then project. Like most organizations, some of your projects will be very specialized, but usually they will break down into common organizational types. 

Once your planning phase is completed, create a test project and experiment to see if the logging works the way you expect. Enroll your project out, and don't forget about automation, infrastructure as code coming in just a few minutes. Remember, the data access logs can be enabled as high in the hierarchy as the organizational level itself.

The benefits of doing this would be a lot of very detailed information on exactly who accessed, edited, and deleted what and when. The drawback is the data access logs can grow to be quite large, and except for free tier or non chargeable logs are build at **$0.50 per gigabyte**. Outside of whatever default logging you institute, every project needs to be analyzed for specialized logging requirements. Infrastructure as code is essentially the process of automating the creation and modifications to your infrastructure using a platform that supports configuration files, which can be put through a CICD pipeline just the same as code. 

Let's look at some of your options for implementing this. Deployment manager is Google Cloud's native infrastructures code technology. It is fully hosted, has Google Support, is editable, and it's state is stored in Google Cloud. Terraform is an open source package from HashiCorp. It isn't hosted directly in Google Cloud, though it is installed by default in Cloud Shell. State management is a decision point for your organization. Options include using Cloud storage, using a Git repo like GitHub, or setting up something local to your organization, or using HashiCorp's pay enterprise service. We discussed the options and benefits of exporting logs. Again, make this part of your plan. 

Start by deciding what, if anything, you will export from aggregated exports at the organization level. Then decide what options you will use, project by project, folder by folder, etc. Then carefully consider your filters, both what they leave in and what they take out. This applies to all logging, not just to exports. Lastly, carefully consider what, if anything, you will fully exclude from logging. Remember though, excluded entries will be gone forever.

Side-channel leakage of data through logs is a common issue. You need to be careful who gets what kind of access to which logs. Remember some of the discussions earlier in this course on monitoring work spaces, and how monitoring a workspace can monitor the current project, or it can monitor up to 100 other projects. This is where your security concerns start. 

Are you monitoring project by chop project or are you selectively grouping work projects into higher level monitored projects? Use appropriate IAM controls on both GCP-based and exported logs, only allowing the minimal access required to get the job done. Especially scrutinize the Data Access Log permissions, as they will often contain personally identifiable information. 

Lastly, let's look at a few access scenarios starting with operational monitoring. These scenarios illustrated typical high level team and their assignments. For example, an organization CTO has high level resource manager organizational admin privileges. That way he or she can assign permissions to the security team and to service accounts. The CTO can then give security team logging viewer permissions so that they may view the Admin Activity logs, and logging private LogViewer permissions, so they may view the data access logs. These permissions would be assigned at the organizational level, so they would be global in scope. 

Access control to data exported to Cloud storage or BigQuery will be controlled selectively with IAM. You might also want to explore using Google Cloud's data loss prevention API to redact personally identifiable information from files and images. Now, let's move on to looking at monitoring for development teams. The security team is unchanged from the last slide. They already have logging viewer and logging private LogViewer from the global assignment. The dev team might be assigned logging viewer permissions at the folder level, so they can see the Admin Activity logs for projects under their development control.

They probably will also need logging, private LogViewer permissions at the dev folder, so that they can see the data access logs. Be sure to limit data, they test with those so they aren't viewing actual customer information. Again, use Cloud storage or BigQuery, IAM to control access to exported logs. Prebuilding dashboards might also be a good option. For external auditors, provide precreated dashboards where possible. If they need broader access, you might make them logging viewer at the org level. For BigQuery, they could be BigQuery data viewer on the exported data set. For Cloud storage, again, you could use IAM, but also remember the temporary access URLs the Cloud storage supports.

## Incident Management

Let's get started.

Let's start with our terminology.

What exactly is an incident?

Is it just an alert?

Imagine you're walking down the street, you step off the curb, and your foot hits the ground awkwardly. The pain sensation you feel is an alert. Pain is the body's way of saying there might be a problem.

You stand a moment on your good leg and you tentatively move your ankle.

Then you apply weight. If everything is okay, you perhaps take another tentative step.

All the while you're thinking, is this bad enough to require me to do something to treat my ankle, or can I safely continue on to normal operations? What you're doing right here is analyzing the situation.

Is it just an alert or does an incident need to be declared?

**An alert indicates that something bad is happening or is on track to happen.**

As we've discussed in this course, something bad is a generic way of saying that one of our service level objectives is being violated, or might be on track to be violated if things are left as they are. An alert doesn't mean your client is yelling at you, in fact, in an ideal world, they may not have noticed anything as yet. 

**An incident is declared at the start of incident response process.** And that's all about what you do next. If you declare an ankle incident, then you might have to go easy, perhaps put ice on it for a while, or you might need to go to the emergency room.

With software systems, you need to try to answer the question, does this alert or pain trigger a response? Or is it simply an anomaly?

In other words, can I safely ignore it or do I need to start some form of incident response? For example, does it need a doctor? Who is it that needs to make that decision? Does the client need to be informed? How about the boss? Does someone need to roll out of bed or can it wait?

All of this should be part of your standard incident response methodology. And this should all kick in when an incident is declared.

Now let's be clear, there's putting out fires by resolving an incident and there's formal incident response.

A lot of organizations are in the ad hoc fix the problem business, but that's not where you want to be.

You want to be one of those pilots pulling out the checklist for some issue to make sure it gets fixed and fixed right. Then, you document it so that it doesn't happen again. And if it does, you have an even better checklist.

You want a **formal incident response methodology** that resolves the incident in a fast, organized way.

Have you ever watched a pilot start a plane, or take off, or deal with an issue in flight?

They have checklists, books of checklists. Checklists for starting the plane, for the takeoff process and for every alert that the aircraft might have. Because every pilot knows if they miss a step or handle an incident improperly, someone could die.

Your system might not be life or death. But your incident response process can learn from how pilots handle things.

**Every incident follows a pattern.** On this slide, you will see a graph with the impact of moving up along the y-axis while time moves along the x.

Start in the lower left, things are going along fine when, bang, alerts start firing. This moves you into the first major incident state, detected.

Something untoward is happening. But that's all you know.

Ouch, you tweaked your ankle.

Triage is started.

How bad is it? What's happening?

And someone needs to make a decision. Is this an incident or a simple anomaly?

Does the ankle need treatment?

That takes you to the **next incident stage, triage.**

Let's say that triage decides that base is something that has to be dealt with. So an incident is declared and an initial severity is set. Your ankle needs a trip to the doctor.

You and your team are working on the problem, the longer it goes on, the worse it gets. Your goal here is to make things better and if possible to fix the problem.

They put a temporary splint on your ankle, ouch. Then get you to the doctor. Unfortunately, the diagnosis is that it's broken.

Let's put it in a cast and get you some crutches, and painkillers would also be nice.

When the patch is in place, your incident moves into the next stage, mitigated. At worst, you've lessened the problem. At best, the client is no longer being impacted.

Now, you do your **root cause analysis.**

Why did this happen?

How can you make sure it never happens again?

Once you can answer those questions, your incident moves into its final phase, resolved.

Pay attention when stepping off curbs.

You write up your post mortem documentation so everyone knows what happened, why, and what you've done to make sure it doesn't happen again. You submit it and move on.

Google learned that **user trust depends on incident response**. Users don't expect perfect service. We all work with technology, and we all know stuff can happen.

Users trust systems that are not only reliable but also consistent in the way that they handle problems.

So to develop and strengthen user trust, your system must minimize outages, respond quickly when things go wrong, and learn from experience. And be transparent with the user, honest and open.

The time to decide how to handle an incident is not when it's happening. You and your organization need to develop a structured incident response methodology.

You want to build an SRE culture on collaboration and clear communication which learns and shares the knowledge, with a predefined and organised information flow. And with a clearly organised approach to client notification.

Once your SRE foundations are in place, then it's time to practice. That way you can be prepared and respond quickly because you'll have the checklists, and you'll know what to do when.

As you and your team train, you'll also learn to be consistent. Like the pilots, you want to reduce duplication of effort and know who's doing what.

Now that we understand the importance of a standard incident response methodology, let's cover some basic principles.

When responding to an incident, maintain a clear chain of command. So team members aren't trying to balance the demands of multiple bosses.

Make sure the incident response team members have clearly defined roles that work in your chain of command.

Keep a working record of debugging and mitigations as you go. Please don't wait until everything is over or it will never get recorded.

Declare incidents early and often. It's better to roll on an incident and discover it was a false alarm, than to let something continue and become exponentially worse.

For your chain of command, start with the incident commander. The commander is the one person in charge of the response.

They delegate necessary tasks to others and coordinate between team members.

If the incident runs longer than the commander's work cycle, handoff carefully, as necessary, until the incident is over.

But commander changes are inherently disruptive, so don't hand off unnecessarily.

Maintain a contiguous easy to read log of the incident state and response state.

Be clear and frequent with your communications, users and stakeholders don't appreciate the mushroom treatment.

Let those affected know you're responding. And make it clear to them who they should contact.

Make sure to keep a record of loose ends as you handle an incident so you won't be leaving them hanging. And don't just fix and move on, prepare a post mortem report with clear information on root causes. Last but not least, update your playbook. Yes, keep a playbook, so your checklists are even better next time you respond to a similar incident.

**Efficient response is built on process, but is also built on drilling.** Drilling makes handling incidents part of a normal process but not a panicked activity.

Should this incident or something you've learned in the incident response become part of a drill?

Here we have some crucial incident management roles. These roles may represent different people or different roles of an individual, depending on team size and incident needs.

Starts at the top, the **incident commander, or IC,** is at the top of the team chain of command. And during an incident, every member of the response team reports to the incident commander.

The IC's role isn't to personally fix the problem, but to make sure that the problem gets fixed.

Think management and organization, not technical wizardry.

IC responsibilities include building and maintaining the team, coordinating the parts of the response, deciding priorities, and delegating activities.

It's the IC that always knows the current status of significant events during the incident. And the IC will also handle communication if a communication lead is not assigned.

Next, we have the **communication lead or CL**. The communication lead is responsible for clear timely communication. The CL keeps everyone outside the response team informed and it's the CL that fields questions and who decides what should be released and through which communication channels. So the CL faces the outside world and lets the team work in peace. Direct communication between the client and other team members should be actively discouraged.

The **operational lead, or OL**, manages the intermediate detailed technical and tactical work of the incident response.

The OL develops and executes the incident action plan, requests additional resources as needed, allocates resources among the various operational response, and maintains close contact with the incident commander, communications lead, primary and secondary responders, and others involved with handling the incident.

The operation lead leads and works closely with the responders.

The primary responder executes the operation lead's technical response plan and maintains close contact with the operations lead and, if there is one assigned, uses the secondary responder for additional technical support. The secondary responder role assist the primary responder if they need help on the particular incident.

How you choose to keep your team and the client informed will depend on your organization, the team and the client.

Dashboards or simple shared documents can be radiators of useful business intelligence and can relay a lot of information very concisely.

A good example of this would be the Google Cloud status page.

More direct and interactive options may include email, SMS, conference calls, chat room, Slack, or any number of third party services such as pager duty.

We said earlier in this module that starting your SRE practices in the middle of an outage is a bad idea. So before the first incident, establish clear criteria for declaring an incident and remember to err on the side of caution. Better false incidents than missed incidents. Choose a communication channel in advance and make sure SRE members and clients know the plan.

Correct templates for communications, templates are easy to fill in, save time, and help avoid missing information.

Train, train, train, train the team on how to respond in theory, and then drill them to make sure they know how to practice what they've learned. Drilling will also help avoid panic.

## Declaring an Incident

Here's where it starts with declaration of an incident. 

When do you declare an incident? Well, it depends. Each organization must define for themselves what constitutes an incident and what's just an anomaly. The secret is **not to wait, but to have clear guidelines** that help operator to determine when to declare an incident. Ambiguity is your enemy here. If operators don't know when to declare an incident, they may do so too late. 

A late declaration of an incident may impair the response teams ability to mitigate the incident and restore service. A corollary to this is that you have to be very careful how you deal with false positives. If you make people afraid of reprisals, things will be missed. 

Example incident triggers include: **specific alerts, alert counts, alert duration, or combinations of the above**. 

Another incident qualifier is **impact assessment**. When an alert fires, answer the questions. What was detected? What does that mean? Is anyone being impacted? If so, who and how? Now that you have the answer to these questions, is this an incident? When you declare an incident, you become the incident commander. On a side note, this should tell you something about the type of person who has the power to declare incidents. Now the process starts; assemble your team, triage the incident, start communicating. 

Once an incident has been declared, the communication lead needs to step up and issue a notice. It doesn't have to be complex, but ideally, it should contain a statement that there's an issue, a list of the impacted services, and if possible, an estimated time to recovery. While writing this slide, I noticed that the Google Cloud status dashboard had a notification which read, "We are currently investigating an issue with instances that utilize Persistent Disk experiencing increased disk latency following a migration event." Simple and to the point.

## Restoring Service

**When an incident is declared, getting service restored is the priority**. You've declared an incident, that usually implies that your system was working but now it is not. Has someone checked the symptoms in the playbook? Is this a known issue with a suggested fix? One common question to ask, is what's changed? Is it possible that the issue is related to the change? If so, can we revert? Connectivity is a common source of issues, is the net workup? Is everything communicating as it should? If not, what can we do? If something is down, do we have a hot or cold replacement. Could we leverage infrastructure as cold, IaC, and spin up a replacement environment? Don't merely let users know there's an issue and not update them until things are fixed. If there are significant knowledge or situational changes, let the user know. 

Here's what's happening. Here's what we know and what's changed, but there are issues that are still ongoing. If there isn't an easy button for the problem, then troubleshoot systematically and divide and conquer your way to success. Segment the problem space so you're working at smaller pieces, and see if those pieces are working, and if one or more could be creating the issue. Step through the system in your mind, thinking about what the parts are doing, and could they be contributing to the bad behavior you are seeing. If something doesn't smell right about one of the parts but there isn't definitive evidence one way or another, add monitoring and logging to prove one way or another. 

If you find a problem, fix it. If not, move on to the next logical piece. Don't get too myopic with your suspect list. Failures, especially intermittent failures, are frequently linked to multiple simultaneous factors. There will be times when there is no easy button and you will have to create bespoke mitigations. Examples may include, failed external dependencies you have no direct control over, or multiple system changes where you can't easily roll back. Ultimately, it doesn't matter. 

You have to do what it takes to restore the service. Once you've stopped the bleeding and mitigating the impact of the incident, **you should inform the stakeholders of the status**. The Communications Lead should notify the stakeholders clearly and know certain terms. The incident has been mitigated, service has been restored, and users are no longer being negatively impacted. But just because the incident has been mitigated, that doesn't mean that the work of the incident response team is complete. No, far from it. Now your team needs to look for the root cause. Troubleshooting and mitigating the incident may have given you a partial understanding of the reason for the outage, but we need specifics. 

Since the fire is out, you have a little breathing room and should be able to investigate multiple possibilities in parallel. Continue to document your progress as you go and keep the team and the stakeholders informed. Consider this example. The service was out. Why was the service out? Because the file system was full. Why was the file system full? Because the person responsible for archiving old files failed to do so. Why didn't they archive the old files? Was the archive tool broken? No, the archive tool wasn't broken. Why didn't they archive the old files? The procedure documentation didn't tell them to archive the files. The root cause here, was a problem with the process documentation. 

**The root cause is always systems, processes, or behaviors**. Blame makes people afraid to bring real issues to light and is detrimental to a learning culture. If the analysis in our example had stopped with because the person responsible for archiving the old files failed to do so, then the person might have been punished without solving the core system problem, which was absent procedures. 

System stability, uptime, and user trust all depend on finding and fixing the core problems. Outages happen, and you can never stop them completely. What you can do, is learn as a team and avoid repeating mistakes. When I say as a team, remember that what's clear to you, might not be clear to everyone on your team. Fix what can be fixed, prevent what can't be fixed, and handle what you can't prevent. Once the incident has been fully resolved and the root cause identified, implement the permanent fix and remove any temporary mitigations, document the resolution, and release a communication. The Communications Lead should notify the stakeholders and uses the root cause analysis has been completed, the incident has been resolved, and we are in the process of documenting it.

## Preventing Recurrence

The incident has been fixed; that's important, but perhaps even more important is preventing it from happening again. The first step towards making sure problems don't reoccur is the postmortem. Remember the earlier example of a pilot's checklist on how to handle incidence? This is where those books originated. 

A **postmortem report** is a detailed and organized document outlining the key events of the incident. It's a blameless report containing the impact, root cause, triggering event, the resolution, any quantifiable metrics, action items, lesson learned, and a timeline. Why bother creating postmortems, especially if the incident has been resolved? Postmortems are letters to your future self and team. They help with future troubleshooting and provide starting points and clarity on future mitigations. This is the origin of those books that airplane pilots can reference. Whenever an incident occurs relating to a particular type of aircraft, it gets documented and possibly included in the next generation of those books. 

Your organization should have the same type of methodology. Your organization should have policies and procedures for writing postmortem reports. This is an important part of the learning process. As a best practice, always write a postmortem whenever an SLO is breached, when an incident requires an emergency response, and when an impacted team requested a follow-up. 

**Your policy should cover how long from incident resolution to draft report unto the final report**. Just like the incident response itself, standardize your documentation. You can find postmortem templates from Google, PagerDuty, Atlassian, and others. Document your troubleshooting process so you'll learn what worked and what didn't, include recommendations for policy changes that should be made, and include a review of the incident response itself. Never stop learning. As stated earlier in this module, practice drills are an excellent way for teams to build experience, refine incident response methodologies, and get used to handling incidence. 

Simulation sessions work like this, an experienced team member presents a scenario based on system knowledge. A great way to do this is to have an incident commander play real-life historical incident. Team members investigate using the system's monitoring and diagnostics, determine the cause, and present strategies for fixing the issue. Use your whole incident response process and practice until it all becomes second nature.

## Debugger

Let's start with the debugger.

**Cloud Debugger** lets you inspect the state of a running application in real time, without stopping or slowing it down. Your users are not impacted while you capture the call stack and variables at any location in your source code. You can use it to understand the behavior of your code in production, as well as analyze it state and find those hard to find bugs.

You can run debugger against code written in a number of modern languages, including Java, Python, Go, Node.js, Ruby, PHP and .NET.

The code can be running in any of Google's compute technologies, including App Engine, Compute Engine, Kubernetes engine, Cloud Run and other places.

Debugger will need access to your application source code. It can pull it from the local file system from Google's cloud source repositories, from the App Engine, or several supported third party source repositories, including GitHub, Bitbucket, and Git Lab. Note, not all features are available in every language and runtime combination.

To use debugger, first enable the Cloud Debugger API in your project, debugger then needs to be enabled in your code. Details of both language specific can be found in the documentation.

Here you can see an example of setting up debugger in a Python application running on App Engine standard.

Start by adding the Google Python Cloud Debugger dependency into your requirements.txt file.

Then as early as possible in your code, perhaps in main.py, import Google Cloud Debugger and use it to enable the debugger.

The Google Cloud compute resource where you're running your code will need permissions to upload telemetry. App Engine and the manage version of Cloud Run, we already have the access that they need.

For Compute Engine VMs, Kubernetes Engine nodes and external systems, create a Service Account with the Cloud Debugger Agent role plus any other access the code will need. And make sure the code runs as that account. The VMs and nodes running under the default Compute Engine service account, not a best practice, we'll need the two scopes mentioned in the current slide.

Please know that the way you enable the debugger for code running in Compute Engine, or external systems is slightly different from the example on the last slide. So please check the documentation

Debugger will also need access to your application source code.

App Engine standard will select the source automatically.

App Engine Flex, Compute Engine, Kubernetes engine, and Cloud Run can also select the source code automatically. But you'll have to provide a source context file in the application route to support this feature.

You can generate the source dash context.json file using the G Cloud command on the slide.

To manually select the source, use the alternative source code page seen on the right.

Unlike traditional debuggers, Cloud Debugger breakpoints do not stop code execution. When the flow of execution passes the breakpoint, a non intrusive snapshot is taken. The snapshot captures local variables and the call stack state at that line, and you can then inspect these at your leisure.

In this example, a breakpoint was set to line 34. When the execution flow passed the break point, a snapshot was taken. You can see the details of the snapshot to the right.

At the bottom, you see the call stack.

Above that, you could expand pets or data to see what those variables contained at that moment in time.

Note, it may take as long as 40 seconds for a new breakpoint to take effect.

Here you can see a close up of the previous slides line 34 snapshot.

Towards the top, note the condition that has been set for the breakpoint. We are telling Debugger only take a snapshot of the value in data dash search is equal to dog.

Below that, notice the expressions that have been created.

Expressions will be evaluated when the snapshot is taken and their values appear below.

You might use an expression to access a variable that's out of the local set, global or static, or to simplify deep navigation.

At the bottom or any local variables, you can use these to inspect your programs variable values at the time the snapshot was taken.

At the bottom of the debugger interface, you'll find a searchable snapshot history panel. Use this when you've set multiple snapshot locations on a file, or to view snapshots that you've already taken.

A snapshot is only taken once. If you want to capture another snapshot of your app state at the same location, you can manually retake it.

One of the many classic techniques for debugging applications is adding log messages. As in, quote, made it this far, or made it this far and x equals. The problem with this approach is that you have to edit the code to do it, and then redeploy the application. Log points allow you to inject logging into running services without editing, restarting or interfering with the normal function of the service. Every time any instances execute code at the log point location, Cloud Debugger logs a message.

The output is sent to the appropriate log for the targets environment. On App Engine, for example, the output is set to the request log in cloud logging.

Log points remain active for 24 hours after creation or until they are deleted or the services redeployed.

In this example, you see we're adding a log point to line 34. If the length of the pets collection is greater than zero, then we create a warning level log entry, searched for, followed by the term in data stash search.

At the very bottom of the interface, you can see the actual logged messages. This is simply a subset of logs viewer.

## Trace

Now let's have a look at Trace. 

Cloud Trace is a distributed tracing system that collects latency data from your applications and displays it in the Google Cloud Console. You can track how requests propagate through your application and receive detailed near real-time performance insights. Cloud Trace automatically analyzes all of your application's traces to generate in-depth latency reports to surface performance degradations. It can also capture traces from all of your VMs, containers, or App Engine products. 

Cloud Trace Agent supports a specific set of mainstream programming languages, but you can also trace using OpenCensus or OpenTelemetry instrumentation. A trace is a collection of spans. A span is an object that wraps metrics and other contextual information about a unit of work in your application. Here we see an example trace created by the call to /probe. It took the /probe span a total of 158 milliseconds to handle the request and return a response. 

But /probe didn't do all the work itself, /probe called a method to RunQuery and datastore which took 66 milliseconds. Then /probe took that result and called next on it, which then took three milliseconds. Finally, /probe called set which took one millisecond. The above four spans all worked to create the single trace that we are examining. 

**Google Trace Overview** is designed to show you a lot of high-level tracing information. This information includes auto-generated performance insights. An example of an insight might be that your application is making too many calls to your datastore which could affect performance. A list of recent traces, list of most frequent URIs and RPC calls, information on changeable spans, and a daily analysis report.

A daily analysis displays up to three auto-generated reports. Each report displays the latency data for the previous day for a single RPC endpoint. If data for an endpoint is available from seven days earlier, that earlier data is included in the graph for comparison purposes. 

A report is generated for an RPC endpoint only if it is one of the three most frequent RPC endpoints, and only if there are at least 100 traces available. If enough data isn't available to create at least one auto-generated report, Trace prompts you to create a custom analysis report. The Trace list window lets you find and examine individual traces in detail. Select a time interval, and you can view and inspect all spans for a Trace, view summary information for a request, and view detailed information for each span and the Trace from this window. To restrict the traces being investigated, you can add filters. 

For example, you can add a filter to display only traces whose latency exceeds one second. Each dot in the latency graph corresponds to a specific request. The XY coordinates for a request correspond to the request time and latency. Clicking a dot will display the Trace details view we saw a couple of slides previously. The recent request table displays the five most recent requests and contains the last 1,000 traces. 

**Analysis reports in Cloud Trace** show you an overall view of the latency for all requests or a subset of requests to your application. This would be similar to the daily report viewed on the Trace Overview main page. 

**Custom report**s can be created for particular request URIs and other search qualifiers. The report can show results as both a density distribution, as in the screenshot above, or as a cumulative distribution. Setting up your code to use Trace is easy. Depending on your language, there are three ways to implement tracing for your applications. 

You can use **OpenTelemetry** and the associated Cloud Trace client library. This is the recommended way to instrument your applications. You can use **OpenCensus** if an OpenTelemetry client library is not available for your language. You can use the Cloud Trace API and right custom methods to send tracing data to Cloud Trace. Be sure you check the documentation for language-specific recommendations. The example on this slide and used in the next lab is written in Python. 

At the time of this writing, Google recommends using OpenCensus with Python. In Python, first, you would import the Trace exporter and the tracer. Then in some initialization section, you would create the exporter and trace your objects to be used later in the code. At this point, RPC spans would be created for your code automatically. Like with using debugger, Trace will need to offload tracing metrics to Google Cloud. App Engine, Cloud Run, Kubernetes Engine, and Compute Engine have default access. 

GCE and GKE get that access through the default Compute Engine service account. For external systems, or GCE and GKE environment that's not running under the default service account, make sure they run under a service account with at least the Cloud Trace Agent role. Trace automatically creates spans for RPC calls. When your application interacts with fire store through the API, that would be an RPC call. 

But you can also create spans manually to enrich the data Trace collects. Here we see an example from a Python Flask web application. When a GET request comes into index.html, we pull a reference to the tracer we've created a couple of slides ago, and we use it to create a new span named index. In the span, we manually create a 0-2 second delay weighted toward zero. We set the result we return on the webpage to tracing requests and end a span.

## Profiler

Now that we can find logic errors and latency bottlenecks in our code, let's profile memory and CPU usage.

Understanding the performance of production systems is notoriously difficult. Attempting to measure performance and test environments usually fails to replicate the pressures on an actual production system.

Continuous profiling of production systems is an effective way to discover where resources like CPU cycles and memory are consumed as a service operates at its working environment.

**Cloud Profiler** is a statistical low overhead profiler that continually gather CPU usage and memory allocation information from your production applications.

It attributes that information to the source code that generated it. Helping you to identify the parts of your application that are consuming the most resources, and otherwise illuminating the performance of your applications characteristics.

The profiling types available vary by language. Check the Google Cloud Profiler documentation for the most recent options.

For the CPU metrics, you will find CPU time is the time the CPU spends executing a block of code. Not including the time it was waiting or processing instructions for something else. And wall time is the time it takes to run a block of code, including all wait time. Including that for locks and thread synchronization. The wall time for a block of code can never be less than the CPU time.

For heap, you have heap which is the amount of memory allocated to the program's heap when the profile is collected. And allocated heap is the total amount of memory that was allocated in the program's heap including memory that has been freed and is no longer in use.

For threads, you have contention, which provides information about the threads stuck waiting for other threads and threads which contains the actual thread counts.

Like with Google's other application performance management products, the exact steps will vary by language. So check the documentation. Here we are sticking with our Python application, which will be running on App Engine. Before you start, make sure the profiler API is enabled in your project.

Start by importing the Google Cloud Profiler package.

Then as early as possible in your code, start the profiler. In this example, we are setting the logging level verbose to 3, or debug level, that will log all messages. The default would be 0 or error only.

At the top of the profiler interface, you can select the profile to be analyzed. You can select by timespan service, profile type, zone, version and other options.

The weight will limit the subsequent flame graph to particular peak consumptions, the top 5% for example. Compare to allows the comparison of two profiles.

Cloud profiler displays profiling data by using flame graphs. Unlike trees and standard graphs, flame graphs make efficient use of screen space by representing a large amount of information in a compact and readable format.

Take a look at this example.

We have a basic application with a main method which calls foo1, which in turn calls bar. Then the main calls foo2, which also calls bar.

As you move through the graphic left to right, you can see how the information is collapsed. First by removing arrows, then by creating frames. And finally, by removing spaces in left aligning.

In the bottom view, you see the result as it would appear in the profiler. If we were looking at CPU time, and you can see the main method takes a total of nine seconds. Below the main bar, you can see how the CPU time was spent. Some in main itself, but most in calls to foo 1 and foo 2, and most of the foo time was spent in bar. So if we can make that bar faster, we could really save some time in main.

Here we have a full example.

We hold the pointer over a frame a tooltip opens and displays additional information, including the functions name, the source file location, and some other metric consumption information.

If you click a frame the graphic is redrawn, making the selected methods call stack more visible

## The Costs of Monitoring

Let's get started with costs. At the time of writing, there are a number of parts of the Google operations suite that are free: debugging and using the Profiler, collecting and using audit and Transparency logs, BigQuery Data Access logs, anything excluded from logs, exporting, noting that what you export may also incur spend, creating and using dashboards, the metrics themselves, Anthos metrics and log streams, App Engine Standard Trace spans and uptime checks. 

Other parts of the operation suite have a free tier to get you started. Examples include, the first 50 gigabytes of logging per project, first 150 megabytes of monitoring data per billing account, the first million monitoring API calls per project, the first 2.5 million trace spans ingested per project, the first 25 million trace spans scanned per project. 

After the free tier, the prices for the various operations related fees, you can see on the slide. For the latest pricing information, please always check the Google documentation. **The networking logs, including VPC Flow, Firewall rules and Cloud NAT will cost you the standard logs storage fees**. But they won't cost you anything extra to generate, provided you are storing them in Cloud Logging. If you export your network telemetry logs to an external service, then the table on the bottom of the slide shows how much the logs will cost to generate above and beyond any destination or networking fees.

## Bill Estimation

Now we've covered some of the pure costs, now let's talk about bill estimation. 

Start by going to your **Billing Account Reports page**. Set your date range and then filter by SKU. The SKUs you want are Log Volume, Spans Ingested, Metric Volume, and Monitoring API Requests. Then check your usage and costs. Note, if the SKU usage is zero for a metric, then it won't appear in the list. Here's another view of the same page. In the chart, you can change the view to display the data daily, monthly, daily cumulative, or monthly cumulative. When you put the chart into a daily cumulative view, you can see the cost trendline does a nice job of showing where we're headed, based on current spend trends. If you recently added login, this may be a good way to estimate what your bill might do. 

You can do the same thing to see how money will be spent in the past months. In this case, the biggest line item is log volume. It begs the question, if I'm spending money on logging data, where exactly is that data coming from? To find the answer, let's check the Metrics Explorer, opening the Monitoring Metrics Explorer and set the resource type to global. Then, depending on what you're looking for, set the metric to one of the following: log bytes ingested provides log bytes ingested in login. 

**Monthly log bytes ingested** provides each point representing the month-to-date sum of log bytes ingested in login. The monthly total is available on the last day of the month, when it is also reset. 

**Metric bytes ingested** provides chargeable numbers of bytes of metrics data ingested in monitoring. 

Trace span ingested provides chargeable trace spans ingested in trace. 

Finally, **monthly trace spans ingested** provides a view of each point represented in the month-to-date sum of trace spans ingested in trace. It resets on the last day of the month. The monthly total is found on the last day of the month. This example was built on a project doing a lot of VM migration work. 

The generic node is all the logging data coming from the Migrate for Compute Engine, formerly Velostrata. The Resource Usage page displays a summary of your logs ingestion data for the selected Google Cloud project. This data includes the previous month's total usage, the current month's to-date usage, and a projected usage for the current month. 

Optionally, to review your project's level billing data in detail, click "See Bill" on the last month's ingested logs volume. This takes you to the Cloud Billing Reports page we saw earlier. Below that, the Log Ingestion table displays a summary of your logs ingestion data by resource type. This data includes the previous month's total usage, the current month's to-date usage, and projected usage for the current month. To view details of the logging usage in a Google project, visit the Logging Resource Usage page. 

The Resource Usage page displays a summary of your logs ingestion data for the selected Google Cloud project. This data includes the previous month's total usage, the current month's to-date usage, and projected usage for the current month. To review your project level billing data in detail, click "See Bill" on the last month's ingested logs volume. This takes you to the Cloud Billing Reports page we saw earlier. Below that, the Logs Ingestion table displays a summary of your logs ingestion data by resource type. This data includes the previous month's total usage, the current month's to-date usage, and projected usage for the current month. 

To see your logs-based metrics usage, head over to Logging, Logs-Based Metrics. Previous month's usage represents the sum of bytes ingested in the logs-based metric in the previous calendar month. Usage, MTD, represents the sum of bytes ingested in the logs-based metric in the current calendar month. Clicking on the column names lets you sort data in ascending or descending order. 

This is helpful, for example, if you want to review which metrics ingest the most data. Early on in this series of modules, we learned that a workspace is used in Cloud monitoring to monitor the resources you care about, whether they are in Google Cloud project, an AWS account, or multiple Google Cloud projects and AWS accounts. To view your monitoring usage by workspace, go to Monitoring, Settings. 

On the Summary tab, the Metrics Ingested table displays a summary of your metrics ingested data by resource. This data includes the previous month's total usage, the current month's to-date usage, and projected usage for the current month. To get your project-level usage in detail, click "View Bill" in the Metrics Ingested table. This takes you to the Cloud Billing Reports page.

## Cost Control Best Practices

Finally, let's discuss some billing best practices. 

Start with what we've already discussed in this module, mostly in the last section. Know your costs, know your operations-related spend, and exactly where it goes. Know what the big ticket items are, know exactly what you're paying for. If you're adding a new type of logging, create alerts to spot runaway spend. In the example, you can see on the current slide, the resource type is global and the metric is monthly log byte is ingested. 

So this is our logging data which thanks to the last slide we know will be built at 50 cents per gigabyte. To get the total amount, the aggregator is set to sum. No reason for this to be super live information, so we set the aligner function to max and the alignment period to 60 minutes. The alert will trigger if anytime series is above some threshold for some period. 

Some products which might **generate substantial logs include Cloud Load Balancing, the Compute Engine logging agents, and write operations in the Cloud Logging API**. Simply exclude what you don't need. If you exclude log entries, you don't pay for them but they're also gone forever. So be careful what you leave in and what you leave out. Also remember, you can exclude a percentage of entries. So another good trick is to do exclusions, but leave enough information that you still get an idea. 

Some common exclusions. Google has several different load balances and they all support various forms of monitoring and/or logging. For load balances that support logging, frequently all you need is a small percentage, exclude 90 percent or more. VPC Flow logs is another good example. Again, you can often exclude 95 percent or more of the entries and still get enough to monitor the VPC. Also remember that your exclusion can filter out entry content. Perhaps you only want to retain logs from sources with the cider outside of your network. 

Another common exclusion is web applications and services kicking out HTTP 200 OK messages. Frequently, OK messages don't provide much insight and they can generate a lot of entries. To reduce metric related charges, watch your agents. Virtual machine monitoring and logging agents both can generate a lot of data. Though installing them is undoubtedly a best practice, there are absolutely going to be exceptions. Weigh the pros and cons and remember that both agents are customizable through configuration files. 

Like the agents, **custom metrics can generate spend**. Newer metrics created using openCensus or openTelemetry support sampling to help produce volume. Metrics also might come from installed third-party libraries like Prometheus or NetApp. They help provide visibility but they also bump up the bill. Don't forget logs-based metrics we previously discussed. Trace charges are based on the number of trace spans ingested and scanned. When latency data is send to trace, it's packaged as a trace that is composed of spans and the spans are ingested by the Cloud Trace backend. When you view trace data, the stored spans are scanned by cloud trace. 

**Trace prices** are based on a number of spans ingested and eventually scanned. Some Google Cloud services such as the App Engine standard environment automatically produce non-changeable spans. Trace has a free allocation per month mentioned previously. Don't be surprised by costs related to manual spans added to App Engine or custom applications. Load balancing spans and logs. 

Since **spans are charged by count**, watch code that generates a lot of spans by looping repeated operations. Log exports themselves are free, but not the targeted resources. Storage phasing and Cloud storage, storage streaming, query phase and BigQuery pops up messages and networking egress fees. Review your usage. The following values are listed resource metrics. GCS bucket, total bytes, BigQuery dataset, stored bytes, pub/sub topic, and byte cost. Be specific with your export filters. 

Also remember you can **exclude and export simultaneously**. That way, the data isn't gone, but it's stored outside of logging. As a point of fact, two terabytes of data access log data stored in logging would cost about $1,000. The same two terabytes stored in regional standard class buckets would cost around $40. With archival, it would be much cheaper. As mentioned earlier, be selective with the logging agent on your virtual machines. Some third-party apps may generate a lot of logging and/or monitoring data. For example, Apache, NGINX, or MySQL. You may want to omit agent installation on selective machines. Most of those apps are still generating the local logs which could be accessed if required. Also, perhaps the logging is enabled on the dev machine but not in production. The logging and monitoring agents are also customizable to exclude or include particular metrics or logs.