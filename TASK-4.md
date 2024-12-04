![image](https://github.com/user-attachments/assets/1b98a405-c64e-4e3f-9ed1-cedcbe577b03)

In this I have solved the Task 4 from the room https://tryhackme.com/r/room/adventofcyber24sidequest

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









8000 port open
c2 server
look in for the github repo

chek this https://github.com/Bloatware-WarevilleTHM/C2-Server/blob/main/app.py repo

app.secret key
was searching a bit what this is about got a article flask one here..

