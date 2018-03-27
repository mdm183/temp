# 2018-03-23 ASA 5585-X Crash in Hemel Production

## *Executive Summary*

We experienced an ASA failover on our Hemel Production new firewall, on the 23/03/2018 at around 10.25am. 
This took our EPG & SRP Networks out of production for approx 30 seconds. The impact at the time was not known


## *Impact*

Teams experienced a connectivity loss across Search, TVS and Pixel services on the Hemel datacentre for approx 30 seconds.
Initially from our gathering we saw spikes of traffic during this time on our backend networks in EPG; This was unsual to us and had the same time of the firewall reporting to failover. 
We initially thought this was due to the traffic loads hitting the firewalls at once and asked the teams to check the services to ensure the applications are running in a normal fashion.

We then had another failure on the 26/03/2018 but this time there was not significant amounts of traffic or any unsual traffic being passed through the firewall.
This happend at around 11.35am and lasted for approx 30 seconds. The Firewall had failover again to its passive partner.

## *Timeline and TTR*

23/03/2018 10.25 | ASA Crashes & Fails over to its passive partner. This causes a network outage on EPG & SRP services for approx 30 seconds. We look further and see unusal amounts of high traffic during this time and report to the teams to check if the applications are running normally. The logs on the firewall tell us there was indeed a failover but doesnt tell us exactly why.

26/03/2018 11.35 | ASA crashes & fails over to its passive partner. This causes a network outage on EPG & SRP services for approx 30 seconds. We look further into this recurring issue and realise this time there is not much traffic being passed, as compared to last time. We start investigating further into this.

26/03/2018 17:30 | We contact Cisco to arrange a special support analyst who can analyse the firewall encoded logs to tell us what exactly is happening that is taking our firewall down.

26/03/2018 21:00 | We are advised that our Firewall IOS verion is 5 years old and we should upgrade in order for them to give us any further debugging support on the crash. The engineer informs us that the crash does seem to be related to the datapath which is responsible for handling all the traffic through the device. We are advised by Cisco to make a remediation change on the config to alter the failover link from being failstate to failover, which reduces the chances for any false positives of failing over to occur between the firewalls. Search and other EPG services initiate a failover to be on 100% Slough for external customer connectivity.

26/03/2018 22:00 | We plan a backup plan to backup all our EPG & SRP contexts configuration and to be ready to bring up the network interfaces on our legacy ASA firewall which is still being used in production for other teams. 

26/03/2018 23:00 | Backup configs are now synchronised with the legacy firewall. We make the remediation change to change the heartbeat mechanism for the firewalls to be on Failover mode instead of Failstate.

26/03/2018 23:15 | Config change is made, the active firewall acknowledges the change, however we loose the passive partner. We are now running on reduced resilliency

27/03/2018 10:45 | We are reported the firewall has gone down agian. As we do not have the secondary partner, the down time is longer at approx 15 mins.

27/03/2018 11:00 | We initiate the backup plan and make the EPG & SRP contexts active on the legacy firewall

27/03/2018 13:00 | We look into the faulty firewall and confirm it has crashed and cannot connect to it in any method. 

27/03/2018 13:10 | We shutdown the primary 10G links across our faulty firewalls, on the core switch.

23/03/2018 13:15 | We log a ticket with DCOPS and ask them to physically power on the crashed firewalls

23/03/2018 13:55 | DCOPS confirm power cycle across the firewalls, we acknowledge the change.

23/03/2018 14:10 | We send out the crashed log output to Cisco for them to further anaylyse the crash.

23/03/2018 18:00 | We are waiting for Cisco to confirm if they can see anything more from the latest crash report sent to them. We are also waiting to hear from another teanm in Cisco who are responsible in advising the best upgrade firmware to move to according to our requirements, taking under account the known bugs for each software version.




## *5 Whys Analysis*

This is a detailed breakdown of the root cause analysis using the 5 Whys method.

This should start with a simple statement of the key feature of the incident. Why is asked, and answered, and from each answer another Why is asked, until a true root cause is identified. Sometimes the analysis will branch so an answer will produce two (or more) Whys, and all paths should be followed to their conclusion. A complex problem with multiple facets could produce several branches, and each should be fully explored. There can be as many Whys as needed. The process is called "5 Whys" because it's often about 5 answers that are needed, but as many as are needed are asked.

The key thing with this analysis is to continue asking Why until a broken or missing process is reached. It is a common pitfall to stop early with an answer that appears satisfactory, but doesn't actually help prevent future recurrence. You want to end up with the broken/missing processes that can be fixed to ensure the issue doesn't arise again. For example, an incident occurs and monitoring doesn't alert because the thresholds are incorrect. You ask, "Why didn't monitoring alert?" and answer with the statement, "Thresholds had not been set correctly". You may be tempted to stop there and fix the threshold, but the truth is that there is a further Why here, asking why the thresholds weren't set correctly - if one hasn't been set correctly, then maybe others are the same and we are still open to the issue again. Assuming the answer now is "Because there is no process for checking monitoring thresholds after initial setup," we have a broken process that can be fixed to eliminate the issue completely.

The layout here is a set of tables, each branch is tracked and gets its own table for its analysis:

State the key issue of the incident here.

|  |  |  |
| ------ | -------- | --- |
| Why 1? | Because 1 |
| Why 2? | Because 2 | Why 2.1? |
| Why 3? | Because 3 | Why 3.1? |
| Why 4? | Because 4 |

Why 2.1

|  |  |  |
| ------ | -------- | --- |
| Why 2.1 | Because 2.1 |
| Why 2.2 | Because 2.2 | Why 2.2.1 |

Why 2.2.1

|  |  |
| ------ | -------- |
| Why 2.2.1? | Because 2.2.1 |
| Why 2.2.2? | Because 2.2.2 |

Why 3.1

|  |  |
| ------ | -------- |
| Why 3.1? | Because 3.1 |
| Why 3.2? | Because 3.2 |

Most 5 Whys analyses are only one parent table with a couple of branches, but there the analysis should include as many as necessary to reach the full set of root cause conclusions.

## *Lessons Learnt*

Put here a list of what has been learnt from the incident. This list should be high level and cover things like changes to operating behaviours or insights into the behaviour of systems or technologies.

## *Corrective Actions*

Put here the list of actions that will be executed to correct the root causes identified in the 5 Whys analysis above. Ideally these should link to the tracking system where those work items are being managed (Jira or github or whereever) so that anyone reading can click straight through and see the efforts underway to deal with the issue and where they stand currently.

The corrective actions are those which are both necessary and sufficient to prevent the incident from recurring. If any corrective actions have associted risk register entries, reference the risk register entry.
