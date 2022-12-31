# CS225CA1

Our system is an integrated medical system for medical treatment(such as sheep placenta injection) that enables registration, queuing, scheduling of treatment, report printing and more. In this system, users have a high degree of freedom over whether or not to receive treatment, and will be scheduled fairly and efficiently. Next, we will introduce the functionality of our system in the following aspects.

![Haha](https://github.com/Erikaqvq/CS225CA1/blob/main/image/qwq.jpeg)


## NOTICE!!! ## 

Before you start testing, here are some information worth noticing!

1. The txt files are automatically generated, containing reports and generated data information.

2. The setting of random data generation is fixed in program, you have to refer to src/myshell.cpp if you want to modify those numbers, as a default, *100* new person will come one day, with some of them randomly do some random choices, possibilties of different issues happening are fixed, you should modify them in code if you want to. 

3. Please don't test illegal inputs with our system, since it's only a **data structure focused project, we will just assume that you are giving legal inputs**! 

	With that in mind, here are the legal inputs for different data:
	1. name: a string within 10 characters.
	2. phone number: a 11 digit number.
	3. wechat: a string within 10 characters.
	4. email: a string within 10 characters.
	5. id: a 6 digit number.
	6. registration station: a number between 1 and the registration station amount.
	7. profession: a number between 0~7, 0 is the highest priority.
	8. risk: 0 for no and low(since they are actually the same in this system), 1 for medimum, 2 for high.
	9. address: lo and la are floats, within 0~1000(slightly more flexible than real longitude and latitude).

4. Please use **Linux system** to run our program!

## System Overview


### Registration

When registering, we can enter the patient's personal information, including identification number, the name and contact details, profession , date of birth, medical risk status.This information will always be stored in the local queue, but our system will upload the information twice a day to the centralized treatment queue for sorting and scheduling. If you want, you can choose to forward data to the centralised treatment queue with any random order.

### Queueing and assignment of appointments

For the registered people, we sort them by the dimensions of profession, ageGroup, risk status, registration time and whether they have a priority letter. Among them, the highest priority is given to the profession, followed by the ageGroup, and then the registration time. For ageGroup, we realize that people's age changes over time, so we track each person's age in real time and update it on their birthday for those who need to change their ageGroup.

For patients who have a priority letter, we will try to meet the deadline for their injection. In order to avoid "traffic jams" caused by many people having the same limited date, we start to prioritize each person five days before their limited date arrives. At the same time, to ensure fairness, we will not allow people with a priority letter to jump directly to the top of the queue, but will allow them to wait in line normally and will only exercise their "priority" rights as a last resort.

For risk status, we have three categories: no risk or low risk, medium risk, and high risk. Patients with no or low risk will be treated normally, patients with medium risk will be deferred for one month (four weeks), and patients with high risk will be considered for treatment only if there is no one else in the queue.

Our system allows patients to choose to withdraw at any stage, but those patients who have previously withdrawn will have a two-week extension at the time of re-registration.Unless they are in the moderate or high risk group.

Of course, our system also supports free modification of basic patient information, and we update the centralised queue when changes in information affect the ordering of treatment.


### Appointment processing

Our system automatically generates a list of people who are scheduled for the next day on each day. In addition, we support modification of injection point capacity, which will take effect from the next day.

After deciding on all the people who should be injected today, each person is assigned a suitable injection site. For each person, we search for a certain number of nearest injection sites with spare capacity in both directions from longitude/latitude, and finally select the nearest injection site to ensure that each person's injection site is not too far from the address. It is also recorded whether the injection is completed or not, and if the patient does not go for the injection at the specified time, it is considered as an automatic withdraw.

### Reporting

Our system will be able to generate three types of reports: daily reports, weekly reports, and monthly reports.

The daily report will show the date and the total number of patients who have been assigned, along with the ids of those patients and their injection sites.

The weekly report will show four different types of patients who have the following activities this week: the people who have been treated, the registered people with a set appointment, the queueing people without a set appointment and those who have withdrawn including their occupational category, age category, risk status, and waiting time. You can choose how to sort these in the initialization step, by name, profession or age group.

The monthly report will show how many people have registered, how many of them are waiting, how many treatment appointments have been made and the number of people who withdrew their registration both in this month and in total. The average waiting time is the average of those who have been treated(time = the difference between the registration date and treated date) and those who are still in line(time = the difference between the registration date and today). Those who have withdrawn will be ignored when calculating the average waiting time.

### Time and Date
In the whole system, we consider the number of days for each month, leap year and hour/minute/second. So, all date and time are legal and correct. The most important function for date calculation is Dif, receiving two dates and returning how many days between the two days. 


### Test

We also write test program to automatically generate information about a large number of registrants while simulating the rapid passage of time. The program will generate random data to test by itself, and print important information as feedback during execution. A command line is provided to user for detailed testing. 

In Linux system, type
	
	make

to compile and generate exe file,

use 

	make run

to run the program. 

#### Available Commands

1. 

	exit

To stop simulation, the whole program will be terminated.

2. 

	setup

Automatically setup a whole new set of test, eliminating all the prevoius data.

3. 

	pass/p

This is the **KEY command** for testing!!! After each passing, the time will be set to 00:00:00 at the begining of the day.

case 1:

	pass/p [n] d

The time will move forward for n days, with random registration, local report, and injection done in each day.

case 2:
	
	pass/p [n] w

The time will be set to the end of this week.

case 3:
	
	pass/p [n] m

The time will be set to the start of the next month.

4. 

	disrand/enrand

Random inputs will/won't be generated each day. With 'disrand', passing days will just not give new assigned patients! Hence you can modify patient information as you like!

5. 

	when/w

Display the simulation time.

6. 

	set/s

Set the infomation of one specific patient. There are lots of supported features here, type the command and read the ouput instruction.

After this command, the available choice are:

	1: withdraw, 2: edit, 3: create, 4: call local update, 5: injection station info

Specificly, in 'edit' the available choices are: 
					
	0: modification done!
	1: name
	2: wechat
	3: email
	4: phone number
	5: address
	6: profession
	7: risk state
	8: priority

To *withdraw* a person, you should use this 'set' command. 

7. 

	generate/g

Generate random inputs for today. 

8. 

	view/v

To view information of a specific person.

9. 

	Fibonacci/F

To see the Fibonacci heap situation right now.

## Specific function introduction

### Registration Record

For people registered, we use class MeTPerson to store information, including name, phone number, Wechat number, id number, email address, birthday date, profession, address, risk status, whether has priority letter and its deadline. If the person inputs a wrong id number or risk status, we will let the person input id number/risk status again. If the person type in it wrongly more than 5 times, we will fail this registration.

### Weekly/Monthly Report
In MeTsystem.cpp, we implemented two procedures weeklyRep_MeT and monthlyRep_MeT. They receive the current time as parameter, and then output a report for this week/month. In weeklyRep_MeT, we output the people who have been treated, the registered people with a settled appointment, the queueing people without a settled appointment and withdraw people. The user can type in a sorting parameter, 0 for sorting printed people list by name, 1 for profession category and 2 for age group.

### Update
We can update the personal information of each patient. The information can be entered manually after selecting the update action in setup. If the information does not affect the determination of priority, such as phone number, wechat, email, we use the updInf function to update it directly. If the information affects the arrangement of injection sites, such as a change in home address, then we use the updAdd function to make changes to the latitude and longitude of the injection sites. If the change is in proffesion and risk status, we use the updPro and updRiskst functions to make the change first, and then call the updKey function in the Fibonacci heap to adjust the whole heap, so that the priority can be updated.

### Withdraw and Reregister
Patients in the central queue can also be selected withdraw, which you can do manually in setup. Patients who are withdrawn will be removed from the Fibonacci heap and their status (tmpSt) will be updated to 3-withdrawn. when such patients are reregistered, their waiting time will be extended by two weeks, unless they are medium risk or high risk. For this case, as well as for the medium-risk one-month deferral, we used a punishtime parameter for documentation.

### Local Queue
We use class RegPos.h to represent a registration position. The dynamic array locList stores unique numbers the system assigned for people who registered at this position. In Regpos.cpp, we use procedure forwardInf to push the people in local queue into centralized queue. In this procedure, cur represents the index of last person who pushed into centralized queue and pushed the remaining people by extracting the unique number and locate the corresponding person in database storing people information. It is worth noting that our local list is a dynamic array, which means that there is no size limit on the local list.


### Central Queue -- Fibheap
For our central queue, we use a Fibonacci heap to implement the ranking of registered patients and the scheduling of treatments based on priority comparisons.

Our Fibonacci heap keeps track of the total number of nodes in the heap, along with a pointer to the smallest (most preferred) node. Each node is a class named nodePerson, and the node contains a keyPerson, a pointer to the MeTPerson class, which can be used to compare sizes, as well as links to its parent, child, and left and right nodes in a bidirectional chain.

The heap has five main functions.

NodePerson* insert(MeTPerson* newp) function can achieve a keyPerson for newp node insert, and return the node. this function can be used to local queue in the patient forward to the central queue.

int removeM() function will remove the smallest node in the Fibonacci heap, while returning the pid value of the keyPerson in that node. The real sorting and priority comparison will be done in this process. Also select the patient with the highest priority to be treated.

void updKey(NodePerson* x, MeTPerson* newp) function can update the keyPerson of node x to newp. Use this function to achieve the update of patient information.

void delNode(NodePerson* x) function can achieve the deletion of node x. It is used to implement the patient withdraw.

void printFib() function prints the internal structure of the Fibonacci heap.

### Priority Rules
For the priority order. We first consider the priority letter. we will first push the patients with the priority letter into the priority queue and sort them according to the DEAD LINE. Each day, we check if there are any patients at the top of the pile whose priority letters will expire within five days, and if so, we schedule these patients for injections first, and then schedule the remaining slots for patients without priority letters. If the number of patients is greater than the maximum capacity of the injection site, only those patients within the capacity will be scheduled for injection and the rest will continue to wait. The reason we choose patients five days before the deadline line is to minimize the possibility of such "traffic jams".

After judging the priority letter, we need to prioritize the remaining factors. In our cmpMeTPer, we first judge the risk status, if a patient is high risk, then his priority should be weaker. Next, we judge the profession. In our system, the smaller the profession, the higher the priority. If the profession is the same, then the age group is judged, and in our system, the smaller the age group, the higher the priority. If the age group is the same, the registration time is judged, and the earlier the registration time is, the higher the priority is.

### Random Local Queue Forward
Our data is stored permanently in the local queue, and our system has a default order when forwarding to the central queue twice a day. But if you want to change the forward order, you can use "s" instruction, then enter 4, and then enter the id of the injection places's order that you want to upload. In other word, the order of random local queue forward will not influence other functionality in our system.

### Lists for the Appointments
We implement this in dailyRep_MeT in MeTsystem.cpp. It generates a daily report. In this procedure, we check withdrawal and age update for today and complete assignment. It outputs a list of people assigned injection tomorrow. 

