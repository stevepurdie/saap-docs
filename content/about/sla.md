# Service Level Agreement (SLA)

`Version: 31 Jan 2023`

This SERVICE LEVEL AGREEMENT ("**SLA**") is by and between **Stakater** and you ("**Customer**"). Each a "Party", and together the "Parties".

## 1. Term

- 1.1 This SLA is effective from the time the Customer uses the Service and will automatically renew every Service Period, unless a Party gives an at least thirty (30) days’ notice of termination in writing to the other Party.

## 2. The Services

- 2.1 Stakater will provide to the Customer the Services as described in the [Covered Service section](#covered-service).

## 3. Service Period

- 3.1 A calendar month is counted as a Service Period. When a period is less than one full calendar month, the period from the day the Customer starts to use the Service to the last day of such month will be counted as one Service Period. For example, if the Customer starts to use the Service on November 19, the first Service Period will be from November 19 to November 30.

## 4. Service Level Objectives

- 4.1 A Service is deemed available when the Customer, with appropriate hardware and sufficient internet bandwidth, can use the Service ("**Uptime**").

- 4.2 Stakater will provide a Service to the Customer with the "**Service Level Objective**" (**SLO**) defined in the [Service Level Objectives section](#service-level-objectives).

- 4.3 Stakater shall use reasonable endeavours to provide the SLO. Stakater monitors a number of specific and generic elements, which collectively enable the Customer to use or access the service.

- 4.4 If Stakater does not meet the SLO in a Service Period, and if Customer meets its obligations under this SLA, Customer will be entitled to, upon request, a "**Financial Credit**" defined in the [Financial Credits section](#financial-credits).

- 4.5 The **SLO** does not apply to free, free trial, or paying Customers who have cancelled and received refunds; software (other than the Service); beta, test, or demo products; or other services that are not part of the Service. No uptime commitment of any kind shall apply to the foregoing Customers, products, or services.

## 5. Downtime

- 5.1 "**Downtime Period**" means a period of at least ten (10) consecutive minutes of Downtime. Intermittent Downtime for a period of less than ten (10) minutes or less will not be counted towards any Downtime Periods.

- 5.2 Subject to the Exclusions detailed in [Section 13](#13-sla-exclusions), the Service will be considered unavailable (and an "**SLA Event**" will be deemed as having taken place) if Stakater’s monitoring detects that the Service or its component has failed for ten (10) consecutive minutes.

- 5.3 Downtime is calculated from the time that Stakater confirms the SLA event has occurred, until the time that Stakater resolves the issue and the Service becomes available to the Customer. If two or more SLA events occur simultaneously, the SLA event with the longest duration will be used to determine the total number of minutes for which the service was unavailable.

## 6. Excused Downtime

- 6.1 Service unavailability shall be excused when due to any of the following ("Excused Downtime"):

    - 6.1.1 "**Scheduled Downtime**" or Downtime resulting from Stakater performing maintenance on the [Covered Service](#covered-service) during a Maintenance Window;

    - 6.1.2 "**Maintenance Window**" or a scheduled period of time when clusters are taken offline for maintenance tasks.

    - 6.1.3 "**Emergency Maintenance**" or any maintenance that Stakater reasonably deems necessary to fix critical problems or patch vulnerabilities that could substantially impair the usability or performance of the Service, to the extent such maintenance cannot reasonably be performed during a Scheduled Downtime or Maintenance Window. Stakater will use commercially reasonable efforts to conduct Scheduled Maintenance and Emergency Maintenance during non-peak hours.

    - 6.1.4 Outage periods due to any cause other than faults by Stakater;

    - 6.1.5 The unavailability of the cloud-based services used by Stakater;

    - 6.1.6 In the case of Stakater having insufficient privileges to the Customers cloud environment/s to perform required maintenance and support tasks;

    - 6.1.7 Due to issues caused by software/application bugs or faults as confirmed by the upstream vendors or product owners that are out of Stakaters control to patch and remediate;

    - 6.1.8 Blocking or throttling by an internet service provider or transit provider;

    - 6.1.9 A denial of service or port attack;

    - 6.1.10 Customer’s intentional acts, errors, or omissions;

    - 6.1.11 Customer’s use of the Service after Stakater has advised Customer to modify Customer’s use of the Service, if Customer did not modify Customer’s use as advised;

    - 6.1.12 Faulty input, instructions, or arguments (for example, requests to access files that do not exist);

    - 6.1.13 Customer’s attempts to perform operations that exceed prescribed quotas or that resulted from Stakater’s throttling of suspected abusive behaviour;

    - 6.1.14 Issues that affect only the Customer and related to external apps or third-parties;

    - 6.1.15 Change requests made by the Customer in which the Customer has been notified concerning the possible impairment and the availability and has agreed to them;

    - 6.1.16 Any actions or inactions from Customer or any third-party;

    - 6.1.17 A force majeure event (including any act of God, natural disaster, fire, riot, act of terrorism or cyber-warfare, act of government, labour strike, epidemic, pandemic) or reasons beyond the control of Stakater to the extent the unavailability could not have been mitigated by implementation of reasonable backup and recovery plans;

    - 6.1.18 Any fault period during which Service is suspended under provision in this SLA.

- 6.2 Customer is solely responsible for obtaining appropriate hardware and internet access to use the Service. The Service shall not be deemed unavailable due to Customer’s inadequate or incompatible hardware and internet access.

## 7. Monthly Uptime Percentage

- 7.1 Monthly Uptime Percentage is calculated as follows: 100% times (x) (i) Total number of minutes in a Service Period, minus (ii) the total Downtime Period in a Service Period minus the Excused Downtime; divided by (iii) the Total number of minutes in the Service Period, represented by the following formula:

    - 7.1.1 In a Service Period, the Service is not available for 30 minutes, half of which is Excused Downtime. The Uptime Percentage was therefore: 100 x [43,200 Minutes – (30-15)] / 43,200 = 99.9653%.

## 8. Financial Credits Request

- 8.1 To request for a Financial Credit, Customer must send an email to Stakater Support with the subject: "**Financial Credit Request**" within thirty (30) days from the time Customer becomes eligible to receive a Financial Credit.

- 8.2 Customer must also provide Stakater with enough information showing loss of external connectivity errors and the date and time those errors occurred. Further, Customer must provide all further information requested by Stakater. If Customer does not comply with these requirements, Customer will forfeit its right to receive a Financial Credit. If a dispute arises with respect to this SLA, Stakater will make a determination in good faith based on its system logs, monitoring reports, configuration records, and other available information.

- 8.3 Stakater will evaluate all information reasonably available to it and make a good faith determination of whether a Financial Credit is owed.

- 8.4 A Financial Credit will be applicable and issued only if the Financial Credit amount for the applicable monthly billing cycle is greater than One US Dollar ($1).

- 8.5 If Stakater determines that a Financial Credit is owed to Customer, Stakater will apply the Financial Credit to Customer’s next invoice. At Stakater’s discretion, Stakater may issue the Financial Credit to the credit card Customer used to pay for the billing cycle in which the Service did not meet the SLO.

## 9. Maximum Financial Credits

- 9.1 The total maximum Financial Credits that can be issued by Stakater to Customer for any and all Downtime that occur in a single Service Period will not exceed twenty percent (20%) of the amount due from the Customer for the [Covered Service](#covered-service) for the Service Period.

## 10. Limitations

- 10.1 Financial Credits will be in the form of a monetary credit applied to future use of the Service and will be applied within sixty (60) days after the Financial Credit was requested. After such times, unused Financial Credits will expire.

- 10.2 Financial Credits may not be applied to variable fees such as overages and transactional fees.

- 10.3 Financial Credits are not available and will be waived where Customer:

    - 10.3.1 Fails to follow the above request procedures; or

    - 10.3.2 Have materially breached this SLA; or

    - 10.3.3 Have invoices totalling more than One Hundred US Dollar ($100) that are more than thirty (30) days past due at either the time of Customer’s request or the time the Financial Credit is to be applied.

- 10.4 Financial Credits will not entitle Customer to any refund or other payment from Stakater.

- 10.5 Financial Credits may not be transferred or applied to any other account.

- 10.6 Customer may not unilaterally offset the fees due from Customer for any performance or availability issues.

- 10.7 Stakater’s failure to meet the SLO or any failure by Stakater to provide uninterrupted service does not constitute a breach of contract. Unless otherwise provided in the SLA, Customer’s sole and exclusive remedy for any unavailability, non-performance, or other failure by Stakater to provide the Service is the receipt of a Financial Credit (if eligible) in accordance with this SLA.

- 10.8 Under no circumstances will any tests performed by Customer, its vendors or partners be recognized by Stakater as a valid measurable criterion of violation length, quality or type for the purposes of establishing a Financial Credit.

## 11. Issues

- 11.1 An "**Issue**" means any event which is not part of the standard operation of the Services and which causes or may reasonably be expected to cause an interruption to, or a reduction in the quality of, the Service.

- 11.2 Stakater must be able to reproduce errors in Issues to resolve them. The Customer will cooperate and work closely with Stakater to reproduce errors, including conducting diagnostic or troubleshooting activities as requested and appropriate. Customer will make its resources available and reasonably cooperate with Stakater to help resolve the Issue.

- 11.3 Stakater is not responsible for comprehensive monitoring of Customer’s data or use of the Service; this responsibility lies with the Customer. Stakater will review the data or circumstances related to the Issue as reported by the Customer.

## 12. Service Improvements

- 12.1 Stakater will make available to customers new versions, releases, and updates to the Service to solve defects and/or errors, keep the Service up-to-date with market developments, or otherwise improve the Service. Stakater will only support the most recent version of the Service.

- 12.2 New versions, releases, or updates will contain at least the level of functionality as set out in this SLA and as contained in the version or release of the Service previously used by Customer, and will not otherwise negatively impact Customer’s use of the Service. Stakater shall make reasonable efforts to ensure that when performing such actions, the impact on Customer and its customer(s) is limited.

## 13. SLA Exclusions

The SLA does not apply to any:

- 13.1 Features designated pre-general availability (unless otherwise set forth in the associated Documentation);

- 13.2 Features excluded from the SLA (in the associated Documentation);

- 13.3 Customer clusters where the deployed version of OpenShift Engine is not offered through the Stable Channel;

- 13.4 Customer workloads;

- 13.5 Errors:

    - 13.5.1 caused by factors outside of Stakater reasonable control (for example, natural disaster, war, acts of terrorism, riots, government action, or a network or device failure external to cloud provider data centres, including at Customer site or between Customer site and cloud provider data centre);

    - 13.5.2 that resulted from Customer's software or hardware or third-party (cloud provider) software or hardware, or both;

    - 13.5.3 that resulted from abuses or other behaviours that violate the SLA;

    - 13.5.4 that resulted from quotas/limits applied by the system;

    - 13.5.5 that resulted from cluster nodes running out of capacity;

    - 13.5.6 that resulted from Customer unauthorised action or lack of action when required, or from Customer employees, agents, contractors, or vendors, or anyone gaining access to Stakater’s solution by means of Customer passwords or equipment, or otherwise resulting from Customer failure to follow appropriate security practices;

    - 13.5.7 that resulted from faulty input, instructions, or arguments (for example, requests to access files that do not exist);

    - 13.5.8 that resulted from Customer attempts to perform operations that exceed prescribed quotas or allowed permissions or that resulted from Stakater’s throttling of suspected abusive behaviour;

    - 13.5.9 that resulted from Customer attempts to perform operations on the account/subscription/etc. being managed by Stakater, even though Customer has permissions they should treat them as read only;

    - 13.5.10 for subscriptions reserved, but not paid for, at the time of the incident.

## Stakater App Agility Platform

Stakater will provide to the Customer the Stakater App Agility Platform ("**SAAP**") and build and manage Customer's OpenShift cluster to allow Customer to optimise its cloud strategy and enable its teams across its organisations to consume Red Hat OpenShift-as-a-Service (the "**Service**").

## Payments

Payment is due once during a Service Period and the Customer will be charged for the number of worker nodes that have been running during the monthly Service Period.

## Covered Service

- "**Covered Service**" means, for each of Zonal Clusters and Regional Clusters, the OpenShift API provided by Customer's cluster(s), so long as the version of OpenShift Engine deployed in the cluster is a version currently offered in the Stable Channel.
- "**Stable Channel**" means the Red Hat OpenShift Container Platform Stable release channel.
- "**Zonal Cluster**" means a single-Zone cluster with control planes (master) running in one Zone (data centre).
- "**Regional Cluster**" means a cluster topology that consists of three replicas of the control plane, running in multiple Zones within a given Region.

## Service Level Objectives

Covered Service | Monthly Uptime Percentage
--- | ---
Zonal Clusters | 99.00%
Regional Clusters | 99.50%

## Financial Credits

If the SLO is not met in a Service Period, and if Customer meets its obligations under this SLA, Customer will be entitled to, upon request, a service Financial Credit equal to the following:

Monthly Uptime Percentage - Regional Cluster | Monthly Uptime Percentage - Zonal Cluster | Financial Credit of the Fees actually paid by the Customer for that Service Period
--- | --- | ---
&gt;99.00% to 99.50% | | 5%
&gt;95.00% to 99.00% | &gt;95.00% to 99.00% | 10%
&lt;95.00% | &lt;95.00% | 20%
