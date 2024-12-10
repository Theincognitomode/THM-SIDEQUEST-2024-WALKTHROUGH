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

I did gpt to find where the source file is located ofcourse sometime gtp helps sometime `/catkin_ws/src/yang/scripts` then I read the code and understood it hows the working flow of it and what it basically does, but before that I tried to run the  start-yang.sh and then I ran `yang.sh` but got the error that the master is not started but both the machines were up, so it seems like we need to hardcode the master in the script for it to work..


![Screenshot 2024-12-07 010257](https://github.com/user-attachments/assets/338aba57-d614-4c90-86f6-1d0ada27fba7)

Ok so this is the most confusing part follow this pattern when getting the flag for yang, and getting yang flag is easy and why is it so will explain (follow the process carefully and one step at a time only..)

1. So on **YIN** you change ros_master_uri to **YIN** ip, then you run roscore and then you run sudo ./yin.sh
2. Now on **YANG** you have to set ros_master_uri to **YIN's** ip, and in /etc/hosts change localhost ip to **YIN's** ip, and now you run sudo ./yang.sh and you should see output coming out of yang.sh, the output will be something like this..

![image](https://github.com/user-attachments/assets/15e26a6f-354d-4d27-98d9-f0a966759e6d)

Now in order to see the traffic for that you will have to use the following command 
```
rostopic
```

Now with the help of this command you will be able to obtain the **RSA PRIVATE KEY**, can't mention the key here try this out yourself..
```
timestamp: "1733594244.9620984"
sender: "Yin"
receiver: "Yang"
action: 1
actionparams: 
  - touch /home/yang/yin.txt
```

Then we get to know that some yin.txt was created by using the touch command I tried to access it but wasn't able to access the file because I was seeing the permission denied message while opening the file, so seems like we need our own directory where we have the access in order to do so what we can do is we will modify the main python file and run that from the home directory where we have the acess of reading the files,

Now why are we first trying to get the **yang** file is becuase if you will read the script you will see that it only takes the private key as the input not the secret.txt which is optional so what I did was i modified the script and in the **os.actions** parameter I mentioned the command something like 
```
cat /root/flag.txt -> /home/yang/yangflag.txt 
```
 For the whole script check the yangscript.py just below this task file..

From the above code we can recover two things 
1. YANG FLAG
2. Secret key
Now if you will read the code carefully on the **YIN** machine you will see that it is mandatory to use the secret.txt which is nothing but a passphrase, but for this you will have to follow the below process :

Yes its confusing but you just simply have to run the script instead of running anything else..

1. So on yin you change ros_master_uri to yin ip, then you run roscore and then you run sudo ./yin.sh
2. Now on yang you have to set ros_master_uri to yin's ip, and in /etc/hosts change localhost ip to yin's ip, and now you we run our custom script.
3. Navigate the directory mentioned there you will see the YANG flag..
```
THM{Yang.also.needs.Yin.to.survive}
```

Now for the **YIN** flag what we will do the same process but we will make **YANG** as the master node and do the following process for it, and you can copy and change the ip from the yinscript.py

1. So on yang you change ros_master_uri to yang ip, then you run roscore and then you run sudo ./yang.sh
2. Now on yin you have to set ros_master_uri to yang's ip, and in /etc/hosts change localhost ip to yang's ip, and now you we run our custom script.
3. Navigate the directory mentione there you will see the YANG flag..
