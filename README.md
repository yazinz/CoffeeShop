# CoffeeShop
# CTF walkthrough
first I ran an nmap scan to identify open ports

![Screenshot_2025-01-05_15_13_51](https://github.com/user-attachments/assets/75303e74-5a52-4f09-a09f-18e9a10dd6b3)

Open http and ssh ports, ok.

![Screenshot_2025-01-05_15_14_32](https://github.com/user-attachments/assets/411b6df5-de01-4882-b6b9-de0f1b189d68)

I tried to identify the CMS used for this machine, its Drupal version 8

![Screenshot_2025-01-05_15_14_39](https://github.com/user-attachments/assets/7e0e2885-27ca-4087-a662-54bfe5ca0307)

I found a ruby exploit for an RCE in Drupal 8.4.5

![Screenshot_2025-01-05_15_16_42](https://github.com/user-attachments/assets/ccf1d03d-0f97-494a-9afa-0bfc2da67635)

But the registry page should not be restricted

![Screenshot_2025-01-05_15_15_11](https://github.com/user-attachments/assets/abf12d63-86d3-4273-b69b-90f6c459fc76)

It looks like its not restricted by the .htaccess file

Lets try the exploit

![Screenshot_2025-01-05_15_18_25](https://github.com/user-attachments/assets/8483c6ab-4d27-48bc-a2ff-fe25097a01da)

I got a reverse shell, but its just a fake shell lets get a real one by the command listed

First we gonna set a listener in a random port with nc :

![Screenshot_2025-01-05_15_19_00](https://github.com/user-attachments/assets/035874f0-4ffa-440c-b757-fdd03cb54160)

Then we gonna curl the vulnerable php file and add our reverse-shell as data for the c parameter

![Screenshot_2025-01-05_15_19_58](https://github.com/user-attachments/assets/6447c54b-176f-45cc-a1dd-f63037e348e5)

we got a shell back as the user www-data but its just an sh shell with no environmental viriables :

![Screenshot_2025-01-05_15_20_05](https://github.com/user-attachments/assets/0e341cf7-f6b6-4f71-9949-753f31fd3d57)

Lets turn it into a bash shell

![Screenshot_2025-01-05_15_20_49](https://github.com/user-attachments/assets/ff8a883e-d290-4368-b459-53bf79a857e8)

Better, Now lets see what we can do to escalate our privilege

I found the users buster and c_developper but I don't have access to theire home directories

![Screenshot_2025-01-05_15_21_35](https://github.com/user-attachments/assets/4b5e7349-6904-4743-8c83-7a2a5f9d723c)

Lets use linpeas to get some informations :

we could use scp to copy linpeas from our computer to the targets computer but we still don't have access to ssh

instead I used python http server :

![Screenshot_2025-01-05_15_22_48](https://github.com/user-attachments/assets/931fe73b-1511-4524-af8f-9fd556cb6ef1)

Now lets get and run our linpeas bash script

![Screenshot_2025-01-05_15_24_58](https://github.com/user-attachments/assets/44713dd6-8728-44a3-8de1-2babd4a36d42)

I found a password.bak file in the /var/backups directory

![Screenshot_2025-01-05_15_26_53](https://github.com/user-attachments/assets/09fa9809-9a90-43e7-a6b0-5cf0d00d07d0)

Lets see if its a real password :

![Screenshot_2025-01-05_15_27_31](https://github.com/user-attachments/assets/98af9b12-bf14-42b0-b25b-5b248f10ebb3)

It worked for the user buster frst try !!

![Screenshot_2025-01-05_15_27_55](https://github.com/user-attachments/assets/cc9acd40-168a-48e5-9938-bcc0b9bed6a9)

After some looking I found a binary with a readme file in buster's Desktop

![Screenshot_2025-01-05_15_28_10](https://github.com/user-attachments/assets/b50bbdf5-2cb0-4b97-ac63-91c5f542e617)

Lets see what this is about

![Screenshot_2025-01-05_15_28_32](https://github.com/user-attachments/assets/7b48a4b9-f584-4b11-9204-96d07924debf)

Hmm intresting its time for binary exploitation

wait its not that hard all it does is it filters some inputs like /bin/bash and /bin/sh but etc... but it doesn't filter an && statement

![Screenshot_2025-01-05_15_30_16](https://github.com/user-attachments/assets/75cd96d7-e8ff-4e1e-b4d1-07a2177726b5)

We got a shell as c_developper Nice !

![Screenshot_2025-01-05_15_30_41](https://github.com/user-attachments/assets/d753d0b9-4137-4502-892b-9927026324af)

Now all we need is a root shell

After runnning sudo -l as you can see there is a miss configuration in the permissions we have the right to run find as root without the password

![Screenshot_2025-01-05_15_30_53](https://github.com/user-attachments/assets/ab97bd9c-4a54-4c0a-a72a-7884d654ac14)

Thanks to the -exec flag we got a root shell

![Screenshot_2025-01-05_15_32_26](https://github.com/user-attachments/assets/d8713caf-2df3-45f0-8686-802ffe707248)

Thanks for reading and playing the CTF.
