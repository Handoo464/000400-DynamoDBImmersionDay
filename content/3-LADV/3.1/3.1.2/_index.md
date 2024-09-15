---
title : "Step 1 - Open the AWS Systems Manager Console"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.1.2. </b> "
---

1. Once you've gained access to the AWS Management Console for the lab, double check the region is correct and the role name **WSParticipantRole** appears on the top right of the console.
2. In the services search bar, search for **Systems Manager** and click on it to open the AWS Systems Manager section of the AWS Management Console.
3. In the AWS Systems Manager console, locate the menu in the left, identify the section **Node Management** and select **Session Manager** from the list.
4. Choose **Start session** to launch a shell session.
5. Click the radio button to select the EC2 instance for the lab. If you see no instance, wait a few minutes and then click refresh. Wait until an ec2 instance with name of `DynamoDBC9` is available before continuing. Select the instance.
6. Click the **Start Session** button (This action will open a new tab in your browser with a new black shell).
7. In the new black shell, switch to the ubuntu account by running `sudo su - ubuntu`
    
    ```bash
    sudo su - ubuntu
    ```
    
8. run `shopt login_shell` and be sure it says `login_shell on` and then change into the workshop directory.
    
    ```bash
    #Verify login_shell is 'on'
    shopt login_shell
    #Change into the workshop directory
    cd ~/workshop/
    ```
    

The output of your commands in the Session Manager session should look like the following:

```bash
$ sudo su - ubuntu
:~ $ #Verify login_shell is 'on'
shopt login_shell
#Change into the workshop directory
cd ~/workshop/
login_shell     on
:~/workshop $
```