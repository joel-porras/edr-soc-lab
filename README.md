# edr-soc-lab

## Project: 
For this lab project, we are simulating a real cyber attack and utlizing endpoint detection and reponse. The attack is delpoyed by an Ubuntu virtual machine (attacker) which will target a Windows endpoint virtual machine (victim). On the Ubuntu VM, we are using Sliver-Server, a command and control (C2) framework designed for adversary and red team testing. On the Windows VM, we are running LimaCharlie, an EDR tool, to capture and take action against malicious activity.



**Tools and Technologies Used:**

- VMware Workstation Pro (Hypervisor)
    - Ubuntu VM (Adversary)
    - Windows 10 VM (Victim)
- LimaCharlie (SecOps Cloud EDR)
- Sliver-Server (C2 Attack Payload Tool)

$~$

## Virtual Machine Setup
We first start off with creating a small virtualization environment for this lab which included setting up an Ubuntu VM and a Windows 10 VM. For the purposes of this lab, Windows Defender was permanently disabled to fully experience a targeted attack without interference.

|![Capture1](https://github.com/user-attachments/assets/381054a4-fafa-4e15-8122-4444a32a2eef)|![Capture2](https://github.com/user-attachments/assets/1eb1318f-901d-44b1-8b69-1ac703205aae)|
|:-:|:-:|

$~$

## LimaCharlie
To set up Lima Charlie as an EDR tool, I first created an organization called ‘Blue Wave Defense’ and selected a preloaded template called “Extended Detection and Response Standard.”

A sensor was then installed on the Windows 10 VM endpoint which serves as the primary data input into LimaCharlie. Once the sensor installation was confirmed successful, the LimaCharlie web UI notifies us if the sensor is detected and reporting in.

After the sensor installation, an “Artifact Collection” rule was created to direct Windows 10 sysmon event logs to LimaCharlie, generating EDR telemetry.

|![Capture3](https://github.com/user-attachments/assets/f6f9f756-ff02-4c54-a2af-f25a02f61e59)|![Capture4](https://github.com/user-attachments/assets/8d9812e2-2be8-494f-aaa5-052d6f5ae440)|
|:-:|:-:|

![Capture5](https://github.com/user-attachments/assets/d784b7b4-0212-4247-b8ba-d589bea4f991)

$~$

## Adversary
To initiate the attack, Sliver-Server is first installed on the Ubuntu VM which is then used to generate a C2 session payload. In this lab, the file name for the C2 session payload generated is called “SHORT_MOUSE.” 

The C2 payload is then dropped and downloaded onto the Windows VM from the Ubuntu VM using a PowerShell command. Once the C2 implant is executed on the Windows VM, a command and control session begins and is verfied by the Ubuntu VM via the Sliver HTTP listener.

With the live session between the two machines, we’re able to engage in active reconnaissance and begin gathering information about the Windows endpoint machine such as host information, file privileges and network connections.

|![Capture5](https://github.com/user-attachments/assets/1cdf067b-2c72-4dc6-b8af-25bdf0c33efc)|![Capture6](https://github.com/user-attachments/assets/6e6b30ec-2507-4c67-80e5-bf5247c08149)|
|:-:|:-:|

![Capture7](https://github.com/user-attachments/assets/547b6660-f53a-4a5c-826d-c87bc87ddf30)

|![Capture8](https://github.com/user-attachments/assets/15171636-a20c-4caa-8e2a-8a6329d9c529)|![Capture9](https://github.com/user-attachments/assets/71415eca-4dfc-419b-a84f-22a0c22a15b0)|
|:-:|:-:|

$~$

## Telemetry

On LimaCharlie, we can identify the payload that was executed within ‘Prcocesses,” based off of the filename and the unusual yellow icon displayed next to it. 

Out of curiousity, a second payload process was executed on the Windows VM to test out some of the options available within LimaCharlie to possible contain or eradicate the malicious activty occuring.

After we identify the second payload process, we can select the ‘Kill Process’ option which then eraidcates the session. We are able to verify the session activty by running a command on the Ubuntu VM which indicates the health status of the second C2 session payload and appears as “Dead.”

![Capture10](https://github.com/user-attachments/assets/5f211274-b843-4aa2-9101-1c79b6813107)

|![Capture11](https://github.com/user-attachments/assets/48d57196-0ca7-4328-8405-a19dca09d7e6)|![Capture12](https://github.com/user-attachments/assets/5045e650-e275-4dad-94f6-db6c2e010916)|
|:-:|:-:|

![Capture13](https://github.com/user-attachments/assets/4eef269d-e2f4-4340-a206-32ddb37d882a)

$~$

## Endpoint Detection

Up to this point, we have now been able to generate a C2 session payload using Sliver-Server and target a Windows endpoint, along with interpreting the telemetry within LimaCharlie. 

We can further escalate attempts to steal credentials on the Windows VM by running a LSASS credential dump. Once this telemetry is available on LimaCharlie, it will appear as an event called “SENSITIVE_PROCESS_ACCESS.” We then create a detection rule called “LSASS Accessed” for this sensitive process.

![Capture14](https://github.com/user-attachments/assets/e7d62023-c1ee-400b-8a8b-eca4e9d88ef9)

![Capture15](https://github.com/user-attachments/assets/f2332204-34aa-49e0-9ccc-e3160039cc53)

|![Capture16](https://github.com/user-attachments/assets/de2b19dc-991b-4400-9d84-6d2819187142)|![Capture17](https://github.com/user-attachments/assets/6ad73d75-439b-4a66-b769-48f752058cca)|
|:-:|:-:|

$~$

## & Response

Not only are we able to create a detection rule to generate an alert on LimaCharlie, we can also create a blocking rule to take immediate action against an attack. 

For this example, we are running a command that aims to delete volume shadow copies which is associated with early signs of a ransomware attack. 

We can view the event activity procces that is executed by this command. For this specific attack, we create a rule which is also known as a “Build D&R Rule” and call it “vss_deletion_kill_it.” With this rule, we’re able block the attack completely and from ever occuring in the future.

![Capture18](https://github.com/user-attachments/assets/f997d3c4-821d-448e-973f-ccdb09b15893)

|![Capture19](https://github.com/user-attachments/assets/86f5f3dc-332b-4738-bb4d-461b178ec76d)|![Capture20](https://github.com/user-attachments/assets/c3f360b8-6f96-4882-a1e6-8c89ac202233)|
|:-:|:-:|

![Capture21](https://github.com/user-attachments/assets/765fa04b-7b25-49cb-9589-d0227714dd32)

$~$

### Credit
Lab project guide by [Eric Capuano](https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro?sd=pf)
