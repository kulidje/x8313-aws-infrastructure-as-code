# X8313 AWS Architect for Real-Time Betting System
The following is a code excerpt from X8313 Inc. and not necessarily fully functional. The purpose of this repo is only to showcase my work and not intended for use.

The code used to represent the AWS stack is built on AWS CloudFormation - AWS's native infastracture-as-code service.

## Real-Time Betting System

**AWS CloudFormation Designer Diagram**
![rt-betting-diagram](https://github.com/kulidje/x8313-aws-infrastructure-as-code/blob/master/pics/RTBetting-designer.png)

**Python Integration (pseudo code)**
![rt-betting](https://github.com/kulidje/x8313-aws-infrastructure-as-code/blob/master/pics/rt-betting.png)

How the system works:

- The "Producer" EC2 Instance is streaming data from the "HorseData" S3 Bucket based on the race-schedule
- When data hits the "Producer" that passes a betting rule, the "Producer" makes a bet-file and queues it in the "Bets" S3 Bucket and sends a notification message to the "TriggerQueue" SQS Message Queue. This notification message includes the bet-file name that was upoaded to "Bets"
- The "Consumer" EC2 Instance is streaming data from the "TriggerQueue". When a notification hits the queue, the "Consumer" looks for the bet-file, using the name in the message, and reads the file into memory. 
- The "Consumer" then tries to replicate that same bet-file. The bet-file name includes the target race identifier as well as the betting strategy, so the "Consumer" knows how to replicate the bets. 
- If the bets from both the "Producer" and "Consumer" match then the bets are sent to the Tote (the Tote is for Horse Racing what the stock-exchange is for the Stock Market)
- There is a Private VPN Connection built between our "Consumer" Instance and the Tote. This was not built via AWS, but through the Public Internet using OpenSwan (an IPsec implementation for Linux) due to the legacy constraints of the out-dated (yet robust) Tote system. 
- Finally, when the bets are succesfully received by the Tote, a bet response is returned to the "Consumer" which is then processed and stored in our "BetLogs" RDS database.
