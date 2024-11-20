# Lets-Defend-Incident-Response-5

**SOC169 - Possible IDOR Attack Detected**

**Summary:**

In this incident response the alert was triggered due to "consecutive requests to the same page". First I check all information given in SIEM. To check this I filtered the firewall logs to show all traffic between destination and source IP. Looking in the logs I notice HTTP POST requests to the destination (WebServer 1005). In these logs it shows the "attacker" using ?user_id=n+1 command to retreive user information. The HTTP response code on all request shows '200' which means it was seuccessful. The HTTP size was different, this gives away that the attack was successful since the names will be different sizes. Endpoint was then isolated and incident was escalated.

**Incident Response:**

First thing is to select `>>` to create a case and take ownership.

<img width="711" alt="1" src="https://github.com/user-attachments/assets/55dd2367-f2a2-480e-aae0-fc59f7cfc5b6">

Start the playbook.

<img width="525" alt="1" src="https://github.com/user-attachments/assets/328263db-3c67-441d-9189-55f957cb1516">

Next is to start collecting data for the investigation.

<img width="499" alt="Screenshot 2024-11-20 083823" src="https://github.com/user-attachments/assets/e38cff7c-9ced-457d-95e1-2c23aae997ab">

I start by noting all the information given in the SIEM.

<img width="701" alt="Screenshot 2024-11-20 083913" src="https://github.com/user-attachments/assets/614fa3f9-7f1c-421d-92e9-f9e8d47b079b">

Why was the alert triggered?

* "consecutive requests to the same page"

Next is to search the logs and see what requests were made.

<img width="678" alt="Screenshot 2024-11-20 085322" src="https://github.com/user-attachments/assets/b40fd706-18e9-41df-9df7-07aacc5d6743">

Looking at the logs: 

<img width="462" alt="Screenshot 2024-11-20 085528" src="https://github.com/user-attachments/assets/14c8a2fb-a591-42e6-a2be-077835ebbac4">
<img width="452" alt="Screenshot 2024-11-20 085559" src="https://github.com/user-attachments/assets/9b8e0331-8d4f-479b-ae06-903532a39261">
<img width="448" alt="Screenshot 2024-11-20 085611" src="https://github.com/user-attachments/assets/21c7ebab-72c0-4cb3-a990-c869531a0dab">
<img width="413" alt="Screenshot 2024-11-20 085621" src="https://github.com/user-attachments/assets/2d678a54-1a2d-4185-a0e5-ace3991b5c1c">
<img width="443" alt="Screenshot 2024-11-20 085633" src="https://github.com/user-attachments/assets/a52e6451-5a1a-4496-bfea-ab5740f15c00">

This shows that the source IP is requesting user_id information, I can also see they were all successful due to a 200 respose code. 
A 200 OK response code is an HTTP status code that indicates a request was successful.

Next is to try to identify the destination IP. First I started with searching internal network and no server was found.

I will submit the source ip to virus total and cisco talos.

This confirmed a few things, this is an outside source trying to obtain user information.

Looking at the 2 websites, I learned that this IP is listed as not trusted and spammy. I also learned the network owner is digital ocean inc.

Next question is to gather some information.

<img width="515" alt="Screenshot 2024-11-20 102611" src="https://github.com/user-attachments/assets/0cec93e5-feca-44fe-8ced-982f92442eb4">

Ownership of the IP addresses and devices.
* Source - 134.209.118.137 - Web Server 1005 - webadmin35
* Destination - 172.16.17.15 - digital ocean inc.

If the traffic is coming from outside (Internet);
* Yes coming form source IP which is outside the network
  
Ownership of IP address (Static or Pool Address? Who owns it? Is it web hosting?)
* Static
  
Reputation of IP Address (Search in VirusTotal, AbuseIPDB, Cisco Talos)
* Poor/ Bad

Next moving into the questions inside the playbook

<img width="521" alt="Screenshot 2024-11-20 103244" src="https://github.com/user-attachments/assets/4d76090b-cb48-4951-91fb-a382fc3c5ba7">

* Yes this investigation has proven to be malicious.

<img width="484" alt="Screenshot 2024-11-20 103437" src="https://github.com/user-attachments/assets/7d8bc5e2-f751-4749-82de-54e2bf72116a">

* This attack, looking at the logs shows consecutive POST request, looking for user information. Proving to be an IDOR attack.

<img width="487" alt="Screenshot 2024-11-20 104608" src="https://github.com/user-attachments/assets/8cf29261-43fe-4488-ba6a-b9206548f9e3">

* To check if this was planned I check email security tab to see if anything was sent to or from the source and destination address.

* Nothing was found

<img width="585" alt="Screenshot 2024-11-20 104546" src="https://github.com/user-attachments/assets/f33db491-ebfa-4c43-a527-b73167407f26">
<img width="598" alt="Screenshot 2024-11-20 104559" src="https://github.com/user-attachments/assets/d0ff6b74-5e0d-43c8-a961-a127f6501816">


Next question is the direction of traffic.

<img width="485" alt="Screenshot 2024-11-20 104742" src="https://github.com/user-attachments/assets/28834ada-b4b7-499f-ae00-73e33cd6c940">

* As seen before this source IP was from outside the internal network.

<img width="524" alt="Screenshot 2024-11-20 104902" src="https://github.com/user-attachments/assets/b0abfb0e-5481-4e27-b0bc-8dbb625b16e4">

* Looking back at the HTTP request size to see if they are all the same size, this would indicate nothing was reached since all the names being the same sie is unlikely. Since the bytes are different this indicates some names are different length than others.
* Yes the attack was successful.

Next step is to contain the endpoint since it ha been identified as compramised.

<img width="573" alt="Screenshot 2024-11-20 110330" src="https://github.com/user-attachments/assets/db07cffa-8fb4-4e73-8139-8147efc62ab0">

* Add Artifacts

<img width="497" alt="Screenshot 2024-11-20 110634" src="https://github.com/user-attachments/assets/555cfe04-1475-48cc-9eb6-5dd20e46a5a5">

* Write someone breif notes on the incident.

<img width="534" alt="Screenshot 2024-11-20 111541" src="https://github.com/user-attachments/assets/af4f3d2a-85cd-45d7-9d2a-4ae67400a06d">

<img width="708" alt="Screenshot 2024-11-20 111639" src="https://github.com/user-attachments/assets/013cde5f-0278-4af5-a01e-1921d0621da3">


