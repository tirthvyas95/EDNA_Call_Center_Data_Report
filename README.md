# EDNA_Call_Center_Data_Report
![Main Page](https://github.com/tirthvyas95/EDNA_Call_Center_Data_Report/blob/0a95575433e4051e7819a5f0e5590dd3d1b4830b/Screenshots/Main%20Page.png)
## Introduciton
Exploratory Data Analysis on a Call Center Dataset provided by Enterprise DNA. This is a portfolio project where we play a role as a data analyst who must create detailed report for senior management for better understanding of the Operations. This Project is part of [Challenges](https://app.enterprisedna.co/app/challenges) published by Enterprise DNA you can learn more on their [Website](https://enterprisedna.co/). 

Our goal here is to act as a report developer where it is our job as an analyst to prepare a report that summarise, the overall service provided by EDNA’s Call Centres(Fictional Call Center) and enabling its senior Management to have a better view of its operations. We are required to utilize the 4 pillars of report development using best practices: data transformation, data modeling, DAX calculations and reporting and visualization.
## Dataset
Here we have a Dataset with Call records of our call center where we have information on the timestamp, duration and call records. Here is the deep dive of the data and the tables that we have
### Call Center Data table
As you can see in the Dataset folder of this repository the data is broken in 4 years from the beginning 2018 to the end 2021, so first we will use the append query function to combine these 4 tables as they have the same column structure and the same type of data.

Call Center Data Table(Fact Table):
1. CallTimestamp = Time stamp when the call was recieved
2. CallType = Type of Call(Dimention Table also Added in the Model)
3. EmployeeID = ID of the Employee who got assigned to the call(Dimention Table also Added in the Model)
4. CallDuration = Duration for which the call lasted
5. WaitTime = Amount of time the customer had to wait
6. CallAbandoned = 1 if abandoned 0 if not
7. CallDate = Date when the call was recieved
8. SLA Compliance = Within SLA if less then 35 sec or Outside SLA

CallCharges Table(Dimention):
1. Call Type Key : Unique number for each call type
2. 

