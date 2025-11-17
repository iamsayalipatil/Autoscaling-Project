# Implementing Autoscalling Group with Load Balancer

## Introduction

In this project, I designed and implemented an Application Load Balancer (ALB) in conjunction with Auto Scaling Groups (ASGs) to efficiently manage traffic distribution and the dynamic scaling of EC2 instances.

The architecture ensures high availability, fault tolerance, and scalability by routing incoming requests across multiple instances and automatically adjusting the number of instances based on real-time demand.

I configured three distinct Auto Scaling Groups to demonstrate different scaling strategies:

1. Home – a static scaling group with a fixed number of instances,

2. Laptop – a dynamic scaling group that reacts to usage metrics (e.g., CPU utilization) to scale in or out automatically.

3. Mobile – a scheduled scaling group that adjusts capacity based on predefined time-based schedules.

Each Auto Scaling Group is linked to its own Target Group, and all Target Groups are registered with the Application Load Balancer, enabling seamless request routing across the infrastructure.


## Deployment Architecture

<p><img src="Images/draw.io.jpeg" alt="Instance"></p>


## System Essentials

1. Application Load Balancer (ALB)

*  Acts as the entry point for incoming traffic and intelligently distributes requests across multiple target groups.

* Enhances fault tolerance and ensures high availability by balancing the load among healthy instances.

2. Auto Scaling Groups (ASGs)

* To showcase various scaling techniques, I configured three distinct Auto Scaling Groups:

   * Home (Static Scaling): Maintains a fixed number of EC2 instances at all times.

   * Laptop (Dynamic Scaling): Automatically scales the number of instances up or down in response to real-time CPU utilization metrics.

   * Mobile (Scheduled Scaling): Adjusts the number of instances based on a predefined schedule (e.g., scaling out during peak traffic hours and scaling in during off-peak times).


3. Target Groups

  * Each ASG is linked to a dedicated target group.

   * These target groups are integrated with the Application Load Balancer, enabling efficient routing of incoming requests to the appropriate set of instances.


## Steps to Setup

### Step 1: Launch Template

* Create 3 Launch Templates: home,laptop and mobile (each with unique User Data scripts)

* In an EC2 Instance, Navigate to the Launch Templates section in your AWS Management Console.

<p><img src="Images/1.1.png" alt="Instance"></p>

* Click on "Create Launch Template" to begin.

* Enter a Template Name (e.g., home, laptop, mobile)



* Write Disription in it.

<p><img src="Images/1.2.png" alt="Instance"></p>

* Select an AMI from the Quick Start options (I selected Amazon Linux).
Alternatively, you can use a custom AMI if you’ve previously created one.

<p><img src="Images/1.3.png" alt="Instance"></p>


* Select a Instance type and Key Pair

<p><img src="Images/1.4.png" alt="Instance"></p>


* Configure the Network Settings: 
Since this is a basic application, only port 80 (HTTP) and port 22 (SSH) are required.
You can either create a new security group with these rules or select an existing security group that already allows this traffic.

<p><img src="Images/1.5.png" alt="Instance"></p>

* Add user datascript in advanced options (you don't need to add this script if u hae selected your own AMI)

<p><img src="Images/1.6.png" alt="Instance"></p>

* Click on Launch Template

* Similarly, Create other templates for Mobile and Laptop

User datascript for mobile-

      #!/bin/bash   
      sudo yum update -y  
      sudo yum install -y httpd  
      sudo systemctl start httpd  
      sudo systemctl enable httpd

      sudo mkdir /var/www/html/mobile
      echo "<h1>This is the mobile page $(hostname -f)</h1>" >
      /var/www/html/mobile/index.html

User datascript for laptop-

      #!/bin/bash   
      sudo yum update -y  
      sudo yum install -y httpd  
      sudo systemctl start httpd  
      sudo systemctl enable httpd

      sudo mkdir /var/www/html/laptop
      echo "<h1>This is the laptop page $(hostname -f)</h1>" >
      /var/www/html/laptop/index.html


<p><img src="Images/1.7.png" alt="Instance"></p>


<p><img src="Images/1.8.png" alt="Instance"></p>

* Sucessfully, we created a Launch Template


<p><img src="Images/1.9.png" alt="Instance"></p>


### Step 2: Creating AutoScalling Group (ASG)


Let’s begin by creating the Auto Scaling Group for home, which uses static Scaling.


1. Navigate to the Auto Scaling Groups section in the AWS Management Console.


2. Click on “Create Auto Scaling Group” to get started.

<p><img src="Images/2.1.png" alt="Instance"></p>

3. You’ll notice there are 7 setup steps listed on the left-hand side of the screen to guide you through the configuration process.


#Choose Launch Template

* Give Name to autoscalling group.

<p><img src="Images/2.2.png" alt="Instance"></p>

* Select a Launch Template of home for home autoscalling group.

* click on next & go to next step.
<p><img src="Images/2.3.png" alt="Instance"></p>

#Choose instance launch option

* Select Availability Zone. (Atleast 2)
* go to next step.
<p><img src="Images/2.4.png" alt="Instance"></p>
<p><img src="Images/2.5.png" alt="Instance"></p>

#Integrate with other services

* We’ll integrate these Auto Scaling Groups with the Load Balancer after all three have been created.
For now, let’s proceed to the next step.
<p><img src="Images/2.6.png" alt="Instance"></p>
<p><img src="Images/2.7.png" alt="Instance"></p>

#Configure Group size and scalling

* For Home (static) ASG select : Desired = 2 , min = 2 , max = 2.

* Here for mobile we are giving : Desired = 3 , min = 2 , max = 7. (After creating mobile ASG we will make it sceduled)

* For Laptop (Dynamic) ASG select : Desired = 3 , min = 2 , max = 7.

<p><img src="Images/2.8.png" alt="Instance"></p>

* select target tracking scaling policy
( if your ASG is static you don't need policy so select No scaling policy)

<p><img src="Images/2.9.png" alt="Instance"></p>

* Go to the next


<p><img src="Images/2.10.png" alt="Instance"></p>

#Add notification

#Add tags

(you dont have to do anything in step 5 and 6 so just click on next)

<p><img src="Images/2.11.png" alt="Instance"></p>

<p><img src="Images/2.12.png" alt="Instance"></p>



#Review

* select create autoscaling group



<p><img src="Images/2.13.png" alt="Instance"></p>


<p><img src="Images/2.14.png" alt="Instance"></p>


* Similarly create other 2 ASG according to their type dynamic and scheduling. Just need to change the group size as i mentioned.

* Only select target tracking scaling policy
( if your ASG is dynamic or scheduled you need policy so select target tracking scaling policy)


* Just need to change the group size as i mentioned.

     * Here, for mobile we are giving : Desired = 3 , min = 2 , max = 7. (After creating mobile ASG we will make it sceduled)

     * For Laptop (Dynamic) ASG select : Desired = 3 , min = 2 , max = 7.



<p><img src="Images/2.15.png" alt="Instance"></p>





### Step 3: Now to make Mobile ASG Sceduled follow following steps 

*  Click on Mobile ASG and select Automatic scaling (You will see the following interface)

<p><img src="Images/3.1.png" alt="Instance"></p>

<p><img src="Images/3.2.png" alt="Instance"></p>



* Scroll down to bottom.


* Click on Create schedule action.

<p><img src="Images/3.3.png" alt="Instance"></p>

* Give name to your schedule.



* Enter Desired , min and max values of instances.

* Choose Cron option in recurrence and give specific time in side box ( sequence is - minute hour date month and day of week)

* Enter the ending time with date

* Click on Create

<p><img src="Images/3.4.png" alt="Instance"></p>
<p><img src="Images/3.5.png" alt="Instance"></p>

* If you click on the Mobile Auto Scaling Group and scroll down to the Scheduled Actions section, you’ll see the scheduled action that has been created.


### Step 4: Creating Target Group For each Autoscalling Group

* Go to Target Groups 


* Click on Create Target Group

<p><img src="Images/4.1.png" alt="Instance"></p>

* Give a Name to your Target Group.

<p><img src="Images/4.2.png" alt="Instance"></p>

* Scroll down and go to health check section.

* Give a path of your application in Health Check Path section.
[Note :For home, i Gave "/" as a path . Here "/ " represten the directry " /var/www/html/ " which is defualt directery of httpd]

* click on next

<p><img src="Images/4.3.png" alt="Instance"></p>


* Click on Create Traget Group (don't need to change anything else)

<p><img src="Images/4.4.png" alt="Instance"></p>

* Similarly Create Target Groups for Laptop and Mobile.

* You only need to change the path in Health check section as given below.



For Laptop :


<p><img src="Images/4.5.png" alt="Instance"></p>



<p><img src="Images/4.6.png" alt="Instance"></p>




For Mobile :


<p><img src="Images/4.7.png" alt="Instance"></p>


<p><img src="Images/4.8.png" alt="Instance"></p>

* Now, three Target Groups are created

<p><img src="Images/4.9.png" alt="Instance"></p>



### Step 5: Connecting Autoscalling group to Target Groups

* Select an Autoscalling Group .
* Click on Action at top right corner.
* Click on Edit
<p><img src="Images/5.1.png" alt="Instance"></p>
* Scroll down and you will see the Load balancing section.

* Click on Application load balancer

* Select a Target group for autoscalling group we selected.
(for laptop ASG select Laptop target group, for mobile ASG select Mobile Target group and for home ASG select home target group.)


<p><img src="Images/5.2.png" alt="Instance"></p>



* click on update.

<p><img src="Images/5.3.png" alt="Instance"></p>


* Successfully connected the Autoscalling group and target groups.

* Repeat process to connect each autoscalling group to its target group.

### Step 6: Setting up an Application Load Balancer and linking it to target groups.

* Go to Load Balancers and Click on Create Load Balancer


<p><img src="Images/6.1.png" alt="Instance"></p>

* Select Application Load Balancer & Click on Create

<p><img src="Images/6.2.png" alt="Instance"></p>

* Give Load balancer name Like ALB


<p><img src="Images/6.3.png" alt="Instance"></p>

* Select all AZ
(choose AZ same as the AZ we selected while creating autoscalling group.)
<p><img src="Images/6.4.png" alt="Instance"></p>


* select a existing security group.
(Select same security group we selected while creating autoscalling group)

<p><img src="Images/6.5.png" alt="Instance"></p>

* In Listners and Routing section,

   * Select forward to target group

   * Choose a default target group. (Here default target group is home-TG)
<p><img src="Images/6.6.png" alt="Instance"></p>

   * Click on Create Load Balancer

<p><img src="Images/6.7.png" alt="Instance"></p>   

* Successfully created Application loadbalancer

* If you scroll down , you will see the HomeTG as defualt target group in Listners and Rules section.

* To add other Target groups..

      * Select HTTP80 i.e., home-TG

      * Click on Manage Rules

      * Click on Add Rule

<p><img src="Images/6.8.png" alt="Instance"></p>   


* Give name to the rule and click on add condition and select path.

<p><img src="Images/6.9.png" alt="Instance"></p>  

* Give path for mobile application

<p><img src="Images/6.10.png" alt="Instance"></p>  

* In Actions section

     * select forward to target group

     * select the target group i.e., mobile-TG

     * Go to next step.

<p><img src="Images/6.11.png" alt="Instance"></p> 


* In Listners rule , give priority 1 for mobileTG

* Click on next

<p><img src="Images/6.12.png" alt="Instance"></p> 

* Click on add rule


<p><img src="Images/6.13.png" alt="Instance"></p>



* Successfully connected Mobile Target Group to Loadbalancer




* Similarly connect Laptop target group to load balancer with priority 2

<p><img src="Images/6.14.png" alt="Instance"></p>


<p><img src="Images/6.15.png" alt="Instance"></p>

<p><img src="Images/6.16.png" alt="Instance"></p>


* You can now see that the active instances have been automatically created in the instance dashboard



<p><img src="Images/7.png.png" alt="Instance"></p>

* To verify if it's functioning correctly:

   * Navigate to the Load Balancers section.

   * Select the load balancer you just created.

   * Scroll down to find and copy the DNS name.

    * Paste the DNS name into your web browser’s address bar and press Enter


<p><img src="Images/DNS Copy.png" alt="Instance"></p>


### Step 7: Result

 * ALB DNS successfully routed traffic to all three target groups.

Output of home:

<p><img src="Images/home op 1.png" alt="Instance"></p>

<p><img src="Images/home op 2.png" alt="Instance"></p>


Output of laptop:
<p><img src="Images/laptop op 1.png" alt="Instance"></p>

<p><img src="Images/laptop op 2.png" alt="Instance"></p>
Output of mobile:
<p><img src="Images/mobile op 1.png" alt="Instance"></p>

<p><img src="Images/mobile op 2.png" alt="Instance"></p>

## Technologies Used

1. Amazon EC2 – Virtual machines used to run the application.

2. Application Load Balancer (ALB) – Distributes incoming traffic to different servers.

3. Launch Templates – Predefined settings used to quickly launch EC2 instances.

4. Auto Scaling Groups (ASG) – Automatically adds or removes instances based on demand (supports fixed, automatic, or scheduled scaling).

5. Target Groups – Directs traffic to the correct group of servers.

6. Amazon Linux – The operating system installed on the EC2 instances.

7. Security Groups – Act like a firewall to control incoming and outgoing traffic to the servers.


## Conclusion

This project showcases how integrating an Application Load Balancer (ALB) with multiple Auto Scaling Groups (ASGs) can effectively deliver high availability, fault tolerance, and scalability within the AWS environment.

By working with Static, Scheduled, and Dynamic scaling methods, I gained practical experience in configuring and managing various scaling techniques. The setup efficiently distributed incoming traffic across target groups, automatically adjusted the number of instances based on demand, and served application requests through a unified DNS endpoint.

In summary, this project strengthened my understanding of AWS load balancing, automated scaling, and cloud infrastructure management.






