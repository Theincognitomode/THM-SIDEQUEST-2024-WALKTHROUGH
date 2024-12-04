![image](https://github.com/user-attachments/assets/1b98a405-c64e-4e3f-9ed1-cedcbe577b03)

In this repo I have solved the **Task 4** from the room https://tryhackme.com/r/room/adventofcyber24sidequest

Lets start with the first thing that is L1 KEY CARD (**hard to get fcard :)** card for sure), for this deploy the room from day 1 of advent cyber event,
While dealing with this room earlier I did a nmap scan but didn't knew this is where the card will be store and how I got to know that lets found out that.

SO there are two appraocehes for that:
1. READ THE SOURCE CODE ON THE GITHUB PAGE
2. NMAP SCAN

Now lets see both the approaches,

Lets start with the **NMAP** approach, here we have the result of the nmap 
![image](https://github.com/user-attachments/assets/918fc478-bd32-408d-a7e6-1d399c6af1bd)

I saw port 8000 was open, then i serached for a bit to look what exactly this port is cabale of doing then i found this intresting article 
![image](https://github.com/user-attachments/assets/ba8514c9-9ba4-4d42-834a-898c4cec708f)

So it can be used as a C2 server for exchanging the data intresting..

Now lets talk about the **Second** method it was exploring the github pages, remeber the link that was given on the walkthrough and there was this guy **"Bloatware-WarevilleTHM"** who commented and mentioned something regarding the C2 server, so I looked into his profile specially the c2 code and look what I found just 

```
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

These were the last line of the code (47-48) so it means the C2 server is active on the port **8000** now lets visit the website and see what we have there..

![image](https://github.com/user-attachments/assets/4e492a70-a93c-4742-a636-21ad930dfa22)
**BINGOO** we found the c2 login portal lets try to log in this site..

Remeber we read the source code ( "https://github.com/Bloatware-WarevilleTHM/C2-Server/blob/main/app.py" ) _if you havent then please go through it_  there was **app.secret_key** any idea what that does, well i also didn't have any idea what it was used for so I searched online and looked for it then i got to know that it is used in flask language to reveal some secrets from stack overflow (https://stackoverflow.com/questions/22463939/demystify-flask-app-secret-key)

So I searched how can this be exploited or how can I use it to unlock the chambers of the C2, while searching i got this article https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/flask#flask-unsign, was really useful, now lets try to replicate this command,
```
flask-unsign --sign --cookie "{'logged_in': True}" --secret 'CHANGEME'
```
Lets see what information we have avaiable we have the secret key, now the we need to look what values to fit in this parameter _{'logged_in': True}_ for this we will refer the code and we can see this
```
session["logged_in"] = True
session["username"] = username
```
So our command will look something like this and make sure we need to access as login so the username will be the admin 
```
flask-unsign --sign --cookie "{"logged_in":True,"username":"admin"}" --secret '@09JKD0934jd712?djD'
```
**Just a quick note if you are using attack box then the command will be different** it will look like this..
![image](https://github.com/user-attachments/assets/a830c573-5884-4952-8ce7-7dc87bf1b67b)

Now we have the cookie, lets add the cookie on the login portal using the **inspect option**

Right click -> Inspect -> Storage -> Cookies -> '+' -> Change the name to **session** -> add the value of the key -> change the path from /login to /data

It should look something like this 
![image](https://github.com/user-attachments/assets/e4029727-2c1f-4795-8476-042165d03ac6)
Press enter then change the link from `<ip>:8000/login` to `<ip>:8000/data`

![image](https://github.com/user-attachments/assets/8b8e7ce9-366d-418b-b65a-bd0abfa761f5)
And **boom baam baam** 🎉🎉

Now take the password from the keycard and use it to unzip the file and take a deep breath ufffff.. (i remeber someone on the chat said you will surely got to know when you have the L1 key and bet he was right about it)

Now lets unzip the file and answer the questions from it...

So we have the first question that is:

**Q1. What is the password the attacker used to register on the site?**

For this what I did was I looked for the POST request because from the question we can say the the request has been posted to login in the server, then i followed the tcp stream and after seraching for around 15 mins i was able to find the answer 

![image](https://github.com/user-attachments/assets/fa8706a4-ae16-44a0-b8b9-2f8ef78b3648)

So the answer is: `QU9DMjAyNHtUaW55X1R`

Now lets move to the next question 

**Q2. What is the password that the attacker captured?**
For this following the same stream for the next 2-3 mins, i got the password that was captured by the attacker...
![image](https://github.com/user-attachments/assets/0c9704a0-fce5-48e7-ba8a-28d15911a2cf)

```
pbnlfVGlueV9TaDNsbF
```

## Now lets move towards the **Q3 AND Q4** 

These were hard to answer man it took almost around 1-2 days to solve and get answers..
**Q3. What is the password of the zip file transferred by the attacker?**

The question mentions that a zip file was transferred intresting so i looked for the zip file in the wireshark, for which i used the following filter..
```
frame contains 50:4b:03:04
```

Let me explain the command it basically says that give me the frame which have the following values in it `50:4b:03:04` this is the starting of the zip file there are mutiple ways to search for the file feel free to use any you want..

Now i had the file but dont know where the password was 😥, then after searching for 2-3 hours then i tried the different approach what i did was i searched for the file like the HTTP object list what exactly was tranfered in this then I got an intresting file the name of the file was **ff** ff hmm sound familiar right? its Frostbite Fox shothand then i downloaded the file and serached what exactly does this file contains 

Lets investigate the file lets see what kind of file is this..
![image](https://github.com/user-attachments/assets/09311f1f-6201-42f3-a548-dab0723dd3c3)

Hmm okk so its ELF 64 executable file lets open this in GHRIDA or you can use IDA feel free to use whatever you like..

Then after spending around 20-30 mins i got a hardcoded value which was something like...

![image](https://github.com/user-attachments/assets/66dd58a1-4f6e-44f2-9999-273f6f6a3806)

Hmm now i had no clue to what to do with this, then after searching a lot for this I end up getting nothing then i tried a new approach which is simply putting the hash value of the file on virus total and then seeing what exactly is the output we have there and the results were intresting...

![image](https://github.com/user-attachments/assets/138ce08a-4676-442b-bdc0-adf34faecc96)

Take a close look on the family labels "rekobe", lets search what is this...

https://www.trendmicro.com/vinfo/us/threat-encyclopedia/malware/backdoor.linux.rekoobe.b -> according to this article its a backdoor that is used in the linux type of this virus is ELF exactly the type of the ff file that we got earlier :0  

So i searched for UNIX BACKDOOR project to get some information about how it works..

Then i came accross this beautiful github repo : https://github.com/orangetw/tsh , but wasnt anything intresting here...

After which I investigated the file and got a intrsting discovery that there was some sort of data that was getting transfered from the port 9001 and from 9002 our zip file was,

Now again the question comes what to do with all this ????

THen i tried to search for a decoder or a analyzer of the rebooke virus.. lucikly there was one (or it was meant to be there ;0)
https://github.com/alexander-utkov/rekobee-analyzer

I downloaded this and then went through the walkthrough of the tool how are we supposed to use it and what can it be used for....



