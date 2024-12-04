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


8000 port open
c2 server
look in for the github repo

chek this https://github.com/Bloatware-WarevilleTHM/C2-Server/blob/main/app.py repo

app.secret key
was searching a bit what this is about got a article flask one here..

