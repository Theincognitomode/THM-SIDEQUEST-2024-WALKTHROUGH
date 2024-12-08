In this repo I have solved the Task 5 from the room https://tryhackme.com/r/room/adventofcyber24sidequest

![image](https://github.com/user-attachments/assets/3a7d8e49-c955-4f22-baf9-042639ba98f8)

So lets start and see what they have to offer us on the day 5 of the AOC quest, its a XXE room and by using that room we can check the content of the apache server,

So in order to retrieve we have two methods
1. By requesting to localhost:8080
2. By manual enumeration

Lets start with the manual enumeration, from the day 5 of the advent event we were taught how we can do XXE and read the files from the directory, I visited the website it was a apache server, had done couple of machine based on apache servers from past so had a basic idea about the directories and mostly where are the config file stored in the easy challenge, i got to know that we have the acess to the /var/www/ssrf/access.log and tried investigating it then see what I found..

![image](https://github.com/user-attachments/assets/4feb9bb6-3d54-4ef5-a675-7ce3ea8d0593)

Lets try visiting the website and see what it has to offer us..
![image](https://github.com/user-attachments/assets/b8a28080-30d9-4d44-ac38-4d602944f5bf)
Well well now we have the card, now lets start both the YIN-YANG machine and see what is needed to be done..
Lets begin with the first step that is scanning the ip address and looking for the serivces it has to offer, well after inital scan didnt find anything intresting just got to know that ssh is possible but seems like theres some other way arond because machine is not allowing the ssh right now, therefore i decided to run the scan for all the ports using the option `-p` and one more key point that _scanning in attackbox is faster as compared to that of our own machine using openvpn_

![image](https://github.com/user-attachments/assets/d73fc89b-33e9-4758-9931-6eecbcaa2414)

Visit the port number 21337 (ring a bell??) there I saw this page, and guess what can be used here to unlock the server the L2 card..
![image](https://github.com/user-attachments/assets/b24ad71e-a7ca-444e-b3c2-38647691c8bf)

Now lets ssh the machine..

Make sure to run both the machines and do the same process on both of it..

At first I though i have to perform some priv escalation task, I was doing that but then I found this..

![image](https://github.com/user-attachments/assets/f1f5619a-b9ea-404a-9ee0-70a30ae3f8c5)

Lets see what that file has to offer..

![image](https://github.com/user-attachments/assets/7901e3ef-d80c-49b7-a96c-780347754af9)

ok so its a script that is triggering some ROS script, in the side quest ROS was mentioned so I went to there website and had a look what exactly it is..

After that I went to see what is in the python script and how it is working..

The file was located at `/catkin_ws/src/yang/scripts` ofcourse sometimes gpt help, used it to understand the working of the script then got to know that a master has to supplied to the script so that they can communicate with each other and share the secret key intresting, so i serached in the `/catkin_ws`



