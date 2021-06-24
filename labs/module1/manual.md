
# Module 1 - Introduction to Containers  

> Duration: 60 minutes

# Module 1: Table of Contents  

[Exercise 1: Running Your First Container](#exercise-1-running-your-first-container)  

[Exercise 2: Working with the Docker Command Line Interface (CLI)](#exercise-2-working-with-the-docker-command-line-interface-cli)  

[Exercise 3: Building custom container images using Dockerfiles](#exercise-3-building-custom-container-images-with-dockerfile)  

[Exercise 4: Interaction with a Running Container](#exercise-4-interaction-with-a-running-container)  

[Exercise 5: Tagging](#exercise-5-tagging)  

[Exercise 6: Building and running SQL Server 2017 in a container](#exercise-6-building-and-running-sql-server-2017-in-a-container)  

### Prerequisites 

* WSL 2 Ubuntu 18.04 
* Windows Terminal
* Visual Studio Code 
* Docker Desktop for Windows 

>Use [this](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers) tutorial for installation and setup instructions


# Exercise 1: Running Your First Container

In this exercise, you will launch a fully functional WordPress blog engine using a Linux-based Docker container. You will learn the commands needed to pull the container image and then launch the container using the Docker CLI. Finally, you will observe that by running the container, you don't need to install any of the WordPress dependencies onto your machine; the WordPress engine binaries and all dependencies will be packaged inside of the container.  



## Running WordPress Blog Engine Container
1. to open a Linux terminal start **Windows Terminal** and choose **Ubuntu 18.04**.

![m1image1](./content/m1image1.png)

1. Type 
```bash
docker pull tutum/wordpress
```


>**Knowledge**: This will tell Docker client to connect to public Docker Registry and download the latest version of the WordPress container image published by tutum (hence the format tutum/wordpress). The container image has been pre-downloaded for you on the VM to save you a few minutes, but you will see each layer that is cached show the text 'Already exists'.

![image4](./content/media/image4.png)

1. Run the command ```docker images``` and notice "tutum/wordpress" container image is now available locally for you to use.  

![mod1image5_2](./content/mod1image5_2.png)

1. That's it! You can now run the entire WordPress in a container. To do that run the command  

    ```bash
    docker run -d -p 8888:80 tutum/wordpress
    ```  

> **Note**: Pay close attention to the dash "-" symbol in front of "-p" and "-d" in the command. The "-d" tells Docker to run the container in the background or *detached*. The "-p" tells Docker to map a port from the client machine to the container. In this case, any data received on port 8888 on your laptop will be forwarded to the container on port 80

![image6](./content/media/image6.png)

1. Run the following ```docker ps``` to see the running containers.

```
root@RRP-6A2QIU3:~# docker ps
CONTAINER ID   IMAGE             COMMAND     CREATED          STATUS         PORTS                                             NAMES
96a793005fae   tutum/wordpress   "/run.sh"   10 seconds ago   Up 9 seconds   3306/tcp, 0.0.0.0:8888->80/tcp   jovial_shaw
root@RRP-6A2QIU3:~#
```

1. Open up your browser and Navigate to [http://localhost:8888](http://localhost:8888) and you should see WordPress.

![m1i9](./content/m1i9.png)

1. Let's launch two more containers based on "**tutum/wordpress**" image. Execute following commands (one line at a time)

    ```bash
    docker run -d -p 8080:80 tutum/wordpress
    
    docker run -d -p 9090:80 tutum/wordpress
    ```

![image10](./content/media/image10.png)

1. Run "**docker ps**" to see all 3 running containers and their port numbers:

```
root@RRP-6A2QIU3:~# docker ps

CONTAINER ID   IMAGE             COMMAND     CREATED          STATUS          PORTS                            NAMES
b6445949e796   tutum/wordpress   "/run.sh"   6 seconds ago    Up 5 seconds    3306/tcp, 0.0.0.0:9090->80/tcp   confident_shockley
2fd55c2251c8   tutum/wordpress   "/run.sh"   53 seconds ago   Up 50 seconds   3306/tcp, 0.0.0.0:8080->80/tcp   great_kapitsa
96a793005fae   tutum/wordpress   "/run.sh"   4 minutes ago    Up 4 minutes    3306/tcp, 0.0.0.0:8888->80/tcp   jovial_shaw
```

1. Now open a new browser window and navigate to URL (using DNS or IP as before) but with port "**8080**" append to it. You can also try port "**9090**". 
    
>Notice that you now have three WordPress blog instances running inside separate containers launched within few seconds. Contrast this to instead creating and running WordPress on virtual machine, which could take significantly more time.

![image12](./content/media/image12.png)


### Congratulations!

You have successfully completed this exercise.

---

# Exercise 2: Working with the Docker Command Line Interface (CLI)

In this exercise, you will learn about common Docker commands needed to work with containers. A comprehensive list of docker commands are available at: [https://docs.docker.com/engine/reference/commandline/docker](https://docs.docker.com/engine/reference/commandline/docker)


## Stopping Single Container
[Return to list of exercises](#module-1-table-of-contents) - [Return to list of modules](#modules)  

1. First list all the containers currently running by executing "**docker ps**" command. You should see list of all running containers. 

    >Notice, the list contains multiple containers based on WordPress image that you ran in the previous exercise.

    ```
    root@RRP-6A2QIU3:~# docker ps

    CONTAINER ID   IMAGE             COMMAND     CREATED          STATUS          PORTS                            NAMES
    b6445949e796   tutum/wordpress   "/run.sh"   6 seconds ago    Up 5 seconds    3306/tcp, 0.0.0.0:9090->80/tcp   confident_shockley
    2fd55c2251c8   tutum/wordpress   "/run.sh"   53 seconds ago   Up 50 seconds   3306/tcp, 0.0.0.0:8080->80/tcp   great_kapitsa
    96a793005fae   tutum/wordpress   "/run.sh"   4 minutes ago    Up 4 minutes    3306/tcp, 0.0.0.0:8888->80/tcp   jovial_shaw
    ```

1. You can stop a running container by using "**docker stop <CONTAINER_ID> or <NAME>**" command. Where CONTAINER_ID and NAME uniquely identify each container listed by the ```docker ps``` command. Your **CONTAINER ID**s will be different
    
    >Note:You can just use the first couple characters to identity the container ID, such as "**b64**" for the screenshot below.

    ```docker stop b64```
    >REPLACE 'b64' with one of your container id's

1. Now run the "**docker ps**" command and notice the listing show one less container running.

    ```
    root@RRP-6A2QIU3:~# docker ps

    CONTAINER ID   IMAGE             COMMAND     CREATED          STATUS          PORTS                            NAMES
    2fd55c2251c8   tutum/wordpress   "/run.sh"   53 seconds ago   Up 50 seconds   3306/tcp, 0.0.0.0:8080->80/tcp   great_kapitsa
    96a793005fae   tutum/wordpress   "/run.sh"   4 minutes ago    Up 4 minutes    3306/tcp, 0.0.0.0:8888->80/tcp   jovial_shaw
    ```


1. If you want see the Container ID of the stopped container, and you forgot the Container ID, you can run "**docker ps -a"** to see all containers, even those that are stopped/exited.

    ![](content/media/image17.png)


## Restart a Container

1. In previous task you issued a Docker command to stop a running container. You can also issue command to start the container which was stopped. All you need is a container ID (same container ID you used earlier to stop a container in previous section), you can also get this using 'docker ps -a'.

1. To start a container run "**docker start <CONTAINER_ID>**". 

    >This uses the container identifier you use in previous section to stop the container.

    ![](content/media/image18.png)

1. To make sure that container has started successfully run "**docker ps**" command. 
    >Notice that WordPress container is now started.

    ![](content/media/image19.png)


## Removing a Container

1. Stopping a container does not remove it and that's the reason why you were able to start it again in the previous task.

    To delete/remove a container and free the resources you need to issue a different command. Please note that this command does not remove the underlying image but rather the specific container that was based on the image. To remove the image and reclaim its resources, like disk space, you'll will need to issue a different command which is covered under the later section "Removing Container Image".

1. To remove a container, ```run docker rm -f <CONTAINER_ID>``` command. 

    This uses the container identifier you used in previous section. If you don't have it handy, simply run "docker ps" and copy the container ID from the listing.

    ![](content/media/image20.png)

    >The "**-f**" switch is used to force the remove operation. It's needed if you are trying to remove a container that is running.


# Stopping All Containers

1. At times you may want to stop all of the running containers and avoid issuing command to stop one container at a time. Run ```docker stop $(docker ps -aq)``` command to stop all running containers. Basically, you are issuing two commands: First the **docker ps** with relevant switches to capture the list of container IDs and then passing the list of IDs to the **docker stop** command.

    
    ![](content/media/image21.png)


## Removing WordPress Container Image

1. Removing a container image form a local system will let you reclaim its disk space. Please note that this operation is irreversible so proceed with caution. In this task you will remove the WordPress container image as you will not be using it any more. You must stop all containers using the image before you can delete the image, unless you use the force parameter.

1. To remove a container image, you'll need its IMAGE ID. Run command "**docker images**". 
    
    ![](content/mod1image22.png)

1. Run the command "**docker rmi <IMAGE_ID> -f**". 

    >Notice the command to remove docker container is "**docker rm**" and to remove an image is "**docker rmi**", with an 'i' for the image. Don't confuse these two commands! The **-f** is to force the removal, you cannot remove an image associated with a stopped container unless you use the force parameter.

    ![](content/media/image23.png)

1. Now, run the command "**docker images**". 

    >Notice that "tutum/wordpress" image is no longer available.

    ![](content/modimage24_1.PNG)

### Congratulations!

You have successfully completed this exercise. Click **Next** to advance to the next exercise.

---

# Exercise 3: Building Custom Container Images with Dockerfile


A Dockerfile is essentially a plain text file with Docker commands in it that are used to create a new image. You can think of it as a configuration file with a set of instructions needed to assemble a new image. In this exercise, you will learn the common commands that go into Dockerfiles by creating custom images based on common technologies like NGINX and ASP .NET Core.  



## Building and Running NGINX Container

1. In this task you will create a new image using the NGINX web server base image hosting a simple static html page. You will start with a Dockerfile with instructions to define its base image, then copy the static html file inside the image and then specify the startup command for the image (using CMD instruction). Later, you will learn how to build the image using Dockerfile and finally will run and test it out.

    The relevant files including static html file **index.html** along with the Dockerfile are available inside the directory **labs/module1/nginx**.  

    ![](content/media/image34.png)

1. Type "**ls**" and press Enter. Notice the available files include "**index.html**" and "**Dockerfile**".

    ![](content/media/image35.png)

1. Let's examine the Dockerfile by typing the command "**nano Dockerfile**" and press Enter. 

    >You can use any other text editor (for example, vi, etc.), but instructions are provided for nano text editor). 
    
    >Note:Notice the structure of Dockerfile.

    ![](content/modimage36.png)  
1. Move your cursor using the arrow keys to the line starting with **Label author="sampleauthor@contoso.com"** and change the text from that to the following: **LABEL author="YourEmail@Email.com"**. Once you are finished making changes press **CTRL + X** and then press **Y** when asked for confirmation to retain your changes. Finally, you will be asked for file name to write. For that press **Enter** (without changing the name of the file). This will close the nano text editor.  

    ![](content/media/image28.png)

1. You are now ready to build a new container image based on the Dockerfile you just modified.  
    Run the command "**docker build -t mynginx .**"

    ![](content/modimage37.png)  

    >Note:Notice how the build command is reading instructions from the Docker file starting from the top and executing them one at a time. The image will download much faster as this is a very small image.

1. If you want to see the layers of an image, you can do "**docker history mynginx**" and see the one you just built. You can also try running this command on other images you have on your VM too.

    ![](content/mod1image38_2.PNG)

1. Run the command "**docker images**"  

    >Note:Notice the new container image appears with the name **mynginx**. Also notice the presence of parent image **nginx** that was pulled from Docker Hub during the build operation. Take a look at the sizes of different images also. This will become important when you build your own custom images to reduce the size for both security and performance.  

    ![](content/mod1image39_2.PNG)
1. Finally, create and run a new container based on "**mynginx**" image. Run command "**docker run -d -p 80:80 mynginx**".

    ![](content/media/image40.png)

1. To test the node app, go to your **Firefox** browser and go to **localhost**.

    ![](content/media/image41.png)


# Building and Running ASP.NET Core 3.x Application Inside A Container

1. In this task you will build ASP .NET Core 3.x application and then package and run it as a container. Change to the relevant directory **labs/module1/aspnetcore**. First, we need to run **dotnet build**, and **publish** to generate the binaries for our application. This can be done manually or by leveraging a **Dockerfile**. In this example, we will run the commands manually to produce the artifacts in a folder called **published**. The **Dockerfile** will only contain instructions to copy the files from the **published** folder into the image.  
     `dotnet build`
    ![](content/mod1image42_2.PNG)   

     `dotnet publish -o published`  
    ![](content/mod1image43_2.PNG)

1. Now that the application is ready, you will create your container image. The Dockerfile is provided to you. View the content of Dockerfile by running the **nano Dockerfile** command. To exit the editor press **CTRL+X**.. 
    >Note:The Dockerfile contents should match the screenshot below:  

    ![](content/mod1image44_2.PNG) 
1. To create the container image run the command   
`docker build -t myaspcoreapp:3.1 .`

    >Note:Notice the **3.1** tag representing the dotnet core framework version.  
    
    ![](content/mod1image45_2.PNG)

1. Launch the container running your application using the command  
`docker run -d -p 8090:80 myaspcoreapp:3.1`  

    ![](content/mod1image46_2.PNG)

    >Note:You are now running ASP.NET Core application inside the container listening at port 80 which is mapped to port 8090 on the host.

1. To test the application, go to **localhost:8090** in your **Firefox** browser.  

    ![](content/mod1image47_2.PNG)

### Congratulations!

You have successfully completed this exercise. Click **Next** to advance to the next exercise.



# Exercise 4: Interaction with a Running Container

In the previous exercise, you were able to build and run containers based on Dockerfiles. However, there may be situations that warrant interacting with a running container for the purposes of troubleshooting, monitoring etc. You may also want to make changes/updates to a running container and then build a new image based on those changes. In this exercise, you will interact with a running container and then learn to persist your changes as a new image.  


[Return to list of exercises](#module-1-table-of-contents) - [Return to list of modules](#modules)    

## Interaction with a Running Container

1. On the command line run "**docker ps**" to list all the currently running containers on your virtual machine.

    ![](content/mod1image48_2.PNG)

    >Note:Notice that multiple containers are running. To establish interactive session a with a running container you will need its **CONTAINER ID** or **NAME**. Let's establish an interactive session to a container based on "**mynodejs**" image. Please note that your **CONTAINER ID** or **NAME** will probably be different. And, unless you specified a name, Docker came up with a random adjective and noun and smushed them together to come up with its own clever name.  

1. Run a command "**docker exec -it <CONTAINER_ID_OR_NAME> bash**" on your **mynodejs container**. 

    >Note: You can run the command **docker exec -it &lt;CONTAINER_ID_OR_NAME&gt; bash** using either the container ID or name.

    >Knowledge: **docker exec** is used to run a command in a running container. The **it** parameter will invoke an interactive bash shell inside the container. 
    
    >Note:Notice that a new interactive session is now establish to a running container. Since "**bash**" is the program that was asked to be executed you now have access to full bash shell inside the container.

    ![](content/mod1image49_2.PNG)

1. You can run a command "**ls**" to view the listing of files and directories. 
    >Note: Notice it has all the files copied by Dockerfile command in previous section.

    ![](content/mod1image50_2.PNG)
    >Knowledge: For more information regarding running commands inside docker container please visit: [https://docs.docker.com/engine/reference/commandline/exec](https://docs.docker.com/engine/reference/commandline/exec)


# Making Changes to a Running Container

While you are interacting and running commands inside a running container, you may also want to make changes/updates to it. Later, you may also create a brand-new image out of these changes. In this task you will make changes to "mynodejs" container image, test them out and finally create a new image (without the need of Dockerfile). 
>Note:Please note that this approach of creating container images is generally used to quickly test various changes, but the best practice to create container images is to use a Dockerfile since it is a declarative file that can be kept in source control repositories.

  First, you will make updates to **server.js** file. You should have an active session already established from previous exercise (if not then please follow the instructions from the previous section to create an active session now).   
  
  Before we can edit the **server.js** file we need to install a text editor. To keep the size of container to a minimum, the **nodejs** container image does not have any extra software installed in the container. This is a common theme when building images and is also the recommend practice.  

1. Before installing any software run the command  
    `apt-get update`  
    >Warning:Note the dash between "apt" and "-get".

    ![](content/mod1image51_2.PNG)

1. To install "**nano**" run a command  
    `apt-get install nano`

    ![](content/mod1image52_2.PNG)

1. After "**nano**" is installed successfully, run the command "**nano server.js**" to open "**server.js**" file for editing.

    ![](content/mod1image53_2.PNG)

1. Use the arrow keys to go to the line starting with "**res.Send(...**" and update the text from "**Hello Node.js!!!**" to "**Hello Node.js AGAIN!!!**". 
    >Note:Your final changes should look like following:

    ![](content/mod1image54_2.PNG)

1. Once you finish making changes press "**CTRL + X**" and then press "**Y**" when asked for confirmation to retain changes. Finally, you will be asked for file name to write. For that press **enter** (without changing the name of the file). This will close the Nano text editor.

1. To save the updates and exit the interactive bash session, run the command "**exit**"

    ![](content/mod1image55_2.PNG)

1. The running container needs to be stopped first and then started again to reflect the changes. Run the command "**docker stop &lt;&lt;CONTAINER ID&gt;&gt;**" to stop the container. Run the command "**docker start &lt;&lt;CONTAINER ID&gt;&gt;**" to start the container.

    ![](content/mod1image56_2.PNG)
1. Finally, to test the update you have made to the container go to **Firefox** and **localhost:8080**. 
    >Note:Notice the output "**Hello Node.js AGAIN!!!**". This verifies that changes to the container were persisted.

    ![](content/mod1image57.png)  


# Interaction with a Running Container

In the previous task you have made changes to running container. However, these changes are only available to that container and if you were to remove the container, these changes would be lost. One way to address this is by creating a new container image based on running container that has the changes. This way changes will be available as part of a new container image. This is helpful during dev/test phases, where rapid development and testing requires a quick turn-around time. However, this approach is generally not recommended, as it's hard to manage and scale at the production level. Also, if content is the only piece that needs to be changed and shared, then using **volumes** may be another viable option. Volumes are covered in module three.   

1. To create new image run the command "**docker commit &lt;CONTAINER ID&gt; mynodejsv2**". 
    >Knowledge:The docker commit command is used to create a new image from a container's changes. 
    
    >Knowledge:If you don't already have it, you can use "**docker ps**" command to get the list of running containers or "**docker ps -a**" to get list of all the containers that are stopped and capture the CONTAINER ID of the container you have updated in previous section.

    ![](content/mod1image58_2.PNG)

1. Now, view the list of all container images by running the command  
    `docker images`   

    >Note:Notice the availability of new image with name "mynodejsv2"

    ![](content/mod1image59_2.PNG) 

    >Note:You now have a container image with the changes you made and tested earlier and is ready to be used.

1. To test the new image run a command  
    `docker run -d -p 8081:8080 mynodejsv2`  

    >Note:This will create a new container based on the image "**mynodejsv2**".

    ![](content/mod1image60.png)  

1. Finally, to test the container, go to **localhost:8081** in **Firefox**. 

    >Note:Notice the text "**Hello Node.js AGAIN!!!**" is returned from the node.js application. This attest that changes were committed properly to new image and hence available to any container created based on the that image.

    ![](content/mod1image61.png)   

### Congratulations!

You have successfully completed this exercise. Click **Next** to advance to the next exercise.



# Exercise 5: Tagging

In this exercise you will learn the role of tagging in container and how to tag new and existing container images using Docker commands.


[Return to list of exercises](#module-1-table-of-contents) - [Return to list of modules](#modules)  

## Tagging Existing Container Image

- In this task you will tag the **mynodejs** container image with **v1**. Recall from the last task that currently this image has the **latest** tag associated with it. You can simply run **docker images** to verify that. When working with container images it becomes important to provide consistent versioning information.  
 
- Tagging provides you with the ability to tag container images properly at the time of building a new image using the **docker build -t imagename:tag .** command. You can then refer to the image (for example inside Dockerfile with **FROM** statement) using a format **image-name:tag**.
 
- If you don't provide a tag, Docker assumes that you meant **latest** and use it as a default tag for the image. It is not good practice to make images without tagging them. You'd think you could assume latest = most recent image version always? Wrong. Latest is just the tag which is applied to an image by default which does not have a tag. If you push a new image with a tag which is neither empty nor 'latest', :latest will not be affected or created. Latest is also easily overwritten by default if you forget to tag something again in the future. **Careful\!\!\!**
 
- When you run **docker images** notice the **TAG** column and pay attention to the fact that all of the custom images created in the lab so far have tag value of **latest**. 

    ![](content/mod1image62_2.PNG)    

- To understand the importance of tagging take a look at the container image created in the previous section **mynodejsv2**. The **v2** at the very end was appended to provide an indicator that this is the second version of the image **mynodejs**. The challenge with this scheme is that there is no inherent connection between the **mynodejs** and **mynodejsv2**. With tagging, the same container image will take the format **mynodejs:v2**. This way you are telling everyone that **v2** is different but has relation to the **mynodejs** container image.  

- Please note that tags are just strings. So, any string including **v1**, **1.0**, **1.1**, **1.0-beta**, and **banana** all qualify as a valid tag.  
 
- You should always want to follow consistent nomenclature when using tagging to reflect versioning. This is critical because when you start developing and deploying containers into production, you may want to roll back to previous versions in a consistent manner. Not having a well-defined scheme for tagging will make it very difficult particularly when it comes to troubleshooting containers.  
 
>Knowledge: A good example of various tagging scheme chosen by Microsoft with dotnet core framework is available at: [https://hub.docker.com/r/microsoft/dotnet/tags](https://hub.docker.com/r/microsoft/dotnet/tags)

1. To tag an existing docker image, run the command "**docker tag &lt;&lt;IMAGE_ID or IMAGE_NAME&gt;&gt; mynodejs:v1**". Replace the IMAGE ID with the image id of "**mynodejs**" container image. To see the updated tag for "**mynodejs**" image run the command "**docker images**".

    ![](content/mod1image63_2.PNG)
    
    >Note:Notice how **latest** and **v1** both exist. **V1** is technically newer, and **latest** just signifies the image that did not have a version/tag before and can feel misleading.  
    Also, note the Image ID for both are identical. The image and its content / layers are all cached on your machine. The Image ID is content addressable, so the full content of it is hashed through a hashing algorithm and it spits out an ID.   
    If the content of any two (or more) images are the same, then the Image ID will be the same, and only one copy of the actual layers are on your machine and pointed to by many different image names/tags.


# Tagging New Container Image

Tagging a new image is done at the time when you build a container image. it's a straightforward process that requires you to simply add the **:tag** at the end of container image name.

1. Navigate to the directory **"labs/module1/nginx"** that contains the "**nginx**" files along with Dockerfile. You can use the command   
     `cd ~/labs/module1/nginx`   

1. Build a new image by running the command  
`docker build -t nginxsample:v1 .`
    >Note:In this case you're creating a new image based on Dockerfile (covered in earlier exercise on NGINX).

    ![](content/mod1image64_2.PNG)
1. If you run a "**docker images**" command, it will list the new container image with tag "**v1**"

    ![](content/mod1image65_2.PNG)

### Congratulations!

You have successfully completed this exercise. Click **Next** to advance to the next exercise.



# Exercise 6: Building and Running SQL Server 2017 in a Container 

 Microsoft SQL Server is one of the most commonly used database server in the market today. Microsoft has made an investment to ensure that customers moving towards containers have an ability to leverage SQL Server through a container image.  
 
Previously Microsoft had released container images for Microsoft SQL Server 2016 for both [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux) and [Windows]((https://hub.docker.com/r/microsoft/mssql-server-windows-express) including [Microsoft SQL Server Express Edition](https://hub.docker.com/r/microsoft/mssql-server-windows-expres) and [Microsoft SQL Server](https://hub.docker.com/r/microsoft/mssql-server-windows) (180 days public preview of Enterprise Edition). Currenlty, SQL Server 2017 is only available for Linux Containers and allow users to bring their own license key when starting the container [https://hub.docker.com/_/microsoft-mssql-server](https://hub.docker.com/_/microsoft-mssql-server)  
 
 In this lab, you will work with Microsoft SQL Server 2017 container image to run a custom database that can store user related information. You will first learn how to associate relevant SQL Server database files to SQL Server container image and how to initialize the database with test data. Then, you will connect a Web Application packaged in another container to the database running inside the SQL Server container. In other words, you will end up with a web application running in a container talking to a database hosted in another container. This is very common scenario, so understanding how it works is important. Let's start by running a SQL Server Express container with the custom database.  


[Return to list of exercises](#module-1-table-of-contents) - [Return to list of modules](#modules)  

## SQL Server 2017 Container Image

1. Make sure you have a Terminal opened, and that you are logged in as root. Also, change the current directory to **labs\module1\sqlserver2017** by using the command  
`cd ~/labs/module1/sqlserver2017`    
1. Before proceeding further, let's remove all the containers from previous tasks. Run the command   
`docker rm $(docker ps -aq) -f`    
1. Look at the **Dockerfile** describing how to package the database  
`cat Dockerfile`    
    ![](content/SqlServerDockerfile.PNG)   
 From the Microsoft SQL Server image, we copy the local files to the container. These local files are composed of:  
**Users.csv** - contains the test data   
    ![](content/mod1image67.png)   
**setup.sql** - the SQL commands to create a database named **LabData** and the **Users** table     
    ![](content/mod1image68.png)   
**entrypoint.sh** - used as an entry point in the **Dockerfile**. It will start the database server and run **import-data.sh**   
    ![](content/mod1image69.png)   
**import-data.sh** - will wait for the server to start and will trigger the database creation, the data import and the ***ping*** command to keep the database alive. Feel free to look at each file we just described to have a better understanding of their role.   
    ![](content/mod1image70.png)   
1. Run the command to build our SQL Server container image   
`docker build -t mysqlserver .`   
    ![](content/mod1image71.png)   
1. Once built, run the start your with the following command (note that we explicitly name our container)    
`docker run -e ACCEPT_EULA=Y -e SA_PASSWORD=P@ssw0rd123! -d -p 1433:1433 --name mydb mysqlserver`  
>Note: The docker run command has various parameters. The following table provides a description for parameters specific to SQL server. See the the [docker hub](https://hub.docker.com/_/microsoft-mssql-server) for an exhaustive list of parameters.   
<table>
  <thead>
  <tr class="header">
  <th>Parameter</th>
  <th>Description</th>
  </tr>
  </thead>
  <tbody>
  <tr class="odd">
  <td>SA_PASSWORD</td>
  <td>The system administrator (userid = 'sa') password used to connect to SQL Server once the container is running. The password in this case is provided in plain text for brevity. However, best practice is to use secrets in Docker: <a href="https://docs.docker.com/engine/reference/commandline/secret">https://docs.docker.com/engine/reference/commandline/secret</a></td>
  </tr>
  <tr class="even">
  <td>ACCEPT_EULA</td>
  <td>Confirms acceptance of the end user licensing agreement found <a href="http://go.microsoft.com/fwlink/?LinkId=746388">here</a>.</td>
  </tr>
  <tr class="odd">
  <td>-e</td>
  <td>Flag that is used to pass environment variables to the container. In this particular case password and license eula are passed as environment variable.</td>
  </tr>
  </tbody>
</table>  

![](content/mod1image72.png)   

6. You can follow the database initialization with the command ***docker logs mydb -f*** until you see the ***ping*** command starting. Once it started, you can interrupt ***docker logs*** by hitting ***CTRL + C***  
    ![](content/mod1image73.png)   
  ...   
    ![](content/mod1image73_2.png)   
    >Note: **LabData** is listed as a new database and that we imported 3 rows (coming from **Users.csv**).  

1. Run the following command to open an interactive session within the database container with **sqlcmd**. **Sqlcmd** is a basic command-line utility provided by Microsoft [https://docs.microsoft.com/en-us/sql/relationaldatabases/scripting/sqlcmd-use-the-utility](https://docs.microsoft.com/en-us/sql/relationaldatabases/scripting/sqlcmd-use-the-utility) for ad hoc, interactive execution of Transact-SQL statements and scripts   
`docker exec -it mydb /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P P@ssw0rd123!`  
    ![](content/mod1image74.png)    
1. Let's begin by listing down all the databases available by running the command  
```sql
SELECT name FROM master.dbo.sysdatabases   
GO
```  
    ![](content/mod1image75.png)   

>Note: **LabData** is listed at the very bottom.   

1. Now let's check that the users we had in **Users.csv** have been properly ingested into the database  at initialization    
```sql
USE LabData  
SELECT * FROM Users  
GO
```   
    ![](content/mod1image76.png)   
1. Now let's exit the **sqlcmd** session. Note that the database will still be running in the background    
`exit`   

## Connect Application to the SQL Server Container   
 
1. Now we will connect an ASP .NET Core Application to our SQL Server and show that it can see the data stored in the database. Change the current directory to **labs/module1/aspnetcorewithsqlserver** by using the command  
`cd ~/labs/module1/aspnetcorewithsqlserver`    
1. First, we need to know what is the IP address of the container running the SQL Server so that the front end web application. Run the following command and note down the IP Address  
`docker inspect mydb`   
    ![](content/mod1image77_3.PNG)   
  ...   
    ![](content/mod1image77_4.PNG)
1. Now we need to update the connection string used by the web app to connect to the SQL Server back end. Open the **Startup.cs** and replace **localhost** in the connection string by the IP address we just copied. Once finish making changes press **CTRL + X** and then press **Y** when asked for confirmation to retain your changes. Finally, you will be asked for file name to write. For that press **Enter** (without changing the name of the file). This will close the nano text editor.     
`nano Startup.cs`    
    ![](content/mod1image78_2.PNG)
1. We are now ready to build the ASP .NET Core web application. Run `dotnet build` to build it.  
    ![](content/mod1image79_2.PNG)   
1. Then publish the artifacts in a **published** folder with  
`dotnet publish -o published`    
    ![](content/mod1image80_2.PNG)  
1. Now, we are ready to build our container that we will tag with an explicit **withsql** string    
`docker build -t myaspcoreapp:3.1-withsql .`     
    ![](content/mod1image81_2.PNG)  
1. Finally, run the container  and expose the web app on port **8082**  
`docker run -d -p 8082:80 myaspcoreapp:3.1-withsql`       
    ![](content/mod1image82_2.PNG)   
1. Open a browser and naviguate to **localhost:8082**. The web app should display the list of users that we ingested in the database.   
    ![](content/mod1image83.png)   
1. Click on **Create New Users** and add a new user name.  
    ![](content/mod1image84.png)   
    >Note: You could remove the front end container and re-run it, you would still see the new user that you have created since it is stored in the SQL Server container.   

1. We have reached the end of the lab, let's remove all the containers to leave the environment in a clean state. Run the command   
`docker rm $(docker ps -aq) -f`     

### Congratulations!

You have successfully completed this module. Click **Next** to advance to the next module.



# Module 5 - Working with Kubernetes MiniKube 

### Duration

45 minutes

# Module 5: Table of Contents    

[Exercise 1: Setup and Configure Minikube on a Virtual Machine](#exercise-1-setup-and-configure-minikube-on-a-virtual-machine)  

[Exercise 2: Working with Replica Sets, Deployments and Health Probes](#exercise-2-working-with-replica-sets-deployments-and-health-probes)  

# Exercise 1: Setup and Configure MiniKube on a Virtual Machine

In this exercise you will setup and configure MiniKube on an Ubuntu VM. MiniKube is a tool that makes it easy to run Kubernetes locally and runs a single-node Kubernetes cluster.

>Note:Note that the virtual machine you are using supports nested virtualization. If you want to use your enterprise or MyVisualStudio (MSDN) account in the future to provision a VM, you must use a Standard\_D2s\_v3 or higher on Azure. The Azure Passes given during this workshop do not provide nested virtualization VMs in Azure.

[Return to list of exercises](#module-5-table-of-contents) - [Return to list of modules](#modules)  

## Install Minikube

1. Sign into your LOD Ubuntu VM.  

1. Open a **terminal** by right clicking anywhere on the Desktop.

    ![](content/media/image66.png)

1. Go into root and type in **@lab.VirtualMachine(UbuntuBase).Password** for the password

    `sudo -i`

1. Run the following commands:

    ```bash
    rm /var/lib/apt/lists/lock
    
    rm /var/cache/apt/archives/lock

    rm /var/lib/dpkg/lock
    ```

1. Navigate to the lab files by running:
`cd labs/module5`  

    ![](content/media/image67.png)  

1. You are now to install Minikube. Run the following two commands (the first command gives execute permission to your script, the second script runs it):

    ```bash
    chmod +x install-minikube.sh
    ./install-minikube.sh
    ```

    ![](content/media/image68.png)
1. Read over the contents of the bash file you just ran while it is installing (the install will take at ~15 minutes. Feel free to start the  Module 5 - Orchestrator Lab on the Windows VM in the meantime):

    ![](content/media/image69.png)
    
    >Note: After minikube installation is completed, the last command in the text file will start the minikube with a single node Kubernetes cluster.
    
    ![](content/media/image70.png)

1. You can always find out more about minikube commands using the help switch, try running it now 
    >Note:Do not worry about the screen color in the screenshots being different from your Ubuntu VM!):

    ```Command-nocopy
    minikube --help
    ```
    
    ![](content/media/image71.png)

    You are now going to create a simple deployment based on ***nginx*** container image and expose it using a service.  

1. Create a deployment and name it ***nginx***

    ```Command-nocopy
   kubectl run nginx --image=nginx --port=80
    ```

1. Expose the deployment using a service   
    `kubectl expose deployment nginx --type=NodePort`  
1. Check that your deployment as well as your service are ready  
    `kubectl get deployment`  
    ![](content/media/image72.png)  
    
    `kubectl get service`   

    ![](content/media/image73.png)

1. Access the nginx default web page using the curl command.  
    `curl $(minikube service nginx --url)`  

    ![](content/media/image74.png)  

1. Clean up the deployment and service with the following commands

    `kubectl delete service nginx`  
    `kubectl delete deployment nginx`

### Congratulations!

You have successfully completed this exercise. Click **Next** to advance to the next exercise.



# Exercise 2: Working with Replica Sets, Deployments and Health Probes


[Return to list of exercises](#module-5-table-of-contents) - [Return to list of modules](#modules)  

## Working with the health probe

In this task you will create a new pod and enable a health probe. To test the probe, pod will run a single container that is going to explicitly fail the health probe request after every 5 probes.

1. You should still be in the **/labs/module5** folder, if not, navigate to there.

1. Create the pod using the yaml file:  
`kubectl apply -f liveness-probe.yaml`  

1. Check the status of the newly created pod. It may take few seconds for the container to be up and running.  
`kubectl get pods`  
    
    >Note: The ***STATUS*** column shows Running and ***RESTARTS*** column have the value zero. That's expected because container is just started, and the health probe has not failed yet.
    
    ![](content/media/image75.png)

1. After 3-4 minutes if you view the status of pods again you should see the RESTARTS column with the value 1 (or higher depending on how long you have waited to check the status of the pod)

    ![](content/media/image76.png)
    
    >Note: If you wait for few more minutes and check the status of pod again you should see the value of RESTARTS column changes to a higher number.

1. Behind the scenes, every time a container fails the health probe it will be restarted again. To get bit more information about the failing health probe run the following command:  
`kubectl describe po liveness-probe`  

    >Note: This describes the pod in detail along with the events that are happening including the failed health probes

    ![](content/media/image77.png)

1. Eventually after failing the health probes multiple times in a short interval container will be put under ***CrashLoopBackOff*** status.  

    ![](content/media/image78.png)

1. You can view the logs from the container that is terminated by using the command:  
`kubectl logs liveness-probe --previous`  
    
    >Note: The sample docker container application is basic so very limited information is available in logs but typically for production ready applications its recommended to write more detailed messages to the logs.

1. Finally, remove the pod  
`kubectl delete pod liveness-probe`  


[Return to list of exercises](#module-5-table-of-contents) - [Return to list of modules](#modules)  

# Working with Replica Set

1. In this task you will first create a replica with predefined labels assigned to pods. Later you will change the labels for a pod and observe the behavior of replica set.

    >Knowledge: A **Replica Set** ensures how many replicas of a pod should be running. It can be considered as a replacement of replication controller. The key difference between the replica set and the replication controller is, the replication controller only supports equality-based selector whereas the replica set supports set-based selector.

    >Note: The ***nginx-rc.yaml*** file is available inside the ***/labs/module5*** subfolder and contains definition of replica set. If you review the content of file you will notice that it will maintain 3 pods with each running nginx container. Pods are also labeled ***app=webapp***.

    To create the replica set and pods run the following command in the **labs/module5** directory.

    `kubectl create -f nginx-rc.yaml`

1. Let's look at the pods along with their labels.  
`kubectl get pods --show-labels`   
    
    ![](content/media/image79.png)  

     You can also list all the replica sets that are available by using the command:  
   `kubectl get replicaset`
    
    ![](content/media/image80.png)

    >Note:Notice we have three pods running. If you delete one of them, replica set will ensure that total pods count remain three and it will do that by creating a new pod.

1. First delete one of the pods 
    >Note: Get name from this command: `kubectl get pods \--show-labels`

    `kubectl delete pod <POD_NAME>`

1. Now, check the pods again. Notice you still have three pods running and one of them is terminating.  

    `kubectl get pods --show-labels`  
    
    ![](content/media/image81.png)  

1. Another factor that plays an important role in determining pods relationship with replica set is the labels. Currently ***app=webapp*** is the selector used by replica set to determine the pods under its watch. If you change the label of a pod from ***app=webapp*** to say ***app=debugging*** then replica set will effectively remove it from its watch and create another pod with the label ***app=webapp***. For replica set its job is to maintain the total count of pods to three as per the definition provided in the yaml file.

    `kubectl label pod <POD_NAME> app=debugging --overwrite=true`   

1. View the pods again and notice that there are four pods running. Replica set created an additional pod immediately after it noticed pod count was less than three.  

    `kubectl get pods --show-labels`  
    
    ![](content/media/image82.png)  

1. Replica set is essentially using selector (defined in the yaml file) to which pods to observe. In this case its label ***app*** matching value ***webapp***. You can also get all the pods with ***app=webapp*** label using the following command.

    `kubectl get pods --show-labels -l app=webapp`

1. Finally remove the replica set using the following command.  

    `kubectl delete replicaset nginx-replica`  

1. As part of the deletion process replica set will remove all the pods that it had created. You can see that by listing the pods and looking at the ***STATUS*** column which shows ***Terminating***.  

    `kubectl get pods`  
    
    ![](content/media/image83.png)

    Eventually pods will be removed. However, if you list the pods again the pod with label ***app=debugging*** is still ***Running***.
    
    `kubectl get pods --show-labels`  
    
    ![](content/media/image84.png)

     Since you have change the label this pod is no longer manage by the replica set. In cases like these you can bulk remove pods based on labels. 
    
    `kubectl delete pods -l app=debugging`  


[Return to list of exercises](#module-5-table-of-contents) - [Return to list of modules](#modules)  

# Working with Deployments

1. In this task you will begin by performing a deployment based on specific version of the nginx container image (v 1.7.9). Later you will leverage a RollingUpdate strategy for deployment to update pods running nginx container image from v1.7.9 to container image 1.8.

    >Note: The **nginx-deployment.yaml** file is available inside the **/labs/module5** subfolder and contains definition of deployment. If you review the content of file you will notice that it will maintain 2 pods with each running nginx container image v1.7.9. Pods are also labeled **app=nginx**.

    Run the following command from the **labs/module5** directory:

    `kubectl create -f nginx-deployment.yaml`  

1. Notice the deployment status by running the command:

    `kubectl get deployment`  

    ![](content/media/image85.png)

1. If you list the pods you should see the out similar to following:

    `kubectl get pods --show-labels`    

    ![](content/media/image86.png)

    >Note: Notice the **LABELS** column and presence of **pod-template-hash** label. This label is used by the deployment during the update process.  

1. You are now going to update the deployment. You are going to update nginx container image from **v1.7.9** to **v1.8**. Before you do that first check the existing definition of the deployment:

    `kubectl describe deployment nginx-deployment`

    ![](content/media/image87.png)

    >Note: Notice the line **Image: nginx:1.7.9** which confirms that the current deployment is using **1.7.9** version of **nginx** image.

1. Perform the update using the command below.  

    `kubectl apply -f nginx-deployment-updated.yaml`  
    
    >Note: If you review the content of **nginx-deployment-updated.yaml** file and compare it with original **nginx-deployment.yaml** the only difference is the image tag which is changed from **1.7.9** to **1.8**.  

1. If you immediately (after step 5) run the command to list all the pods you should see output like following:

    `kubectl get pods --show-labels`  

    ![](content/media/image88.png)

    >Note: Notice that the deployment strategy of rolling update ensures that the old pods (nginx **v1.7.9**) are terminated only after new pods (nginx image **v1.8**) are in a running state. Also notice that the label **pod-template-hash** values are different for old and new pods. This is because the pod definition (due to change of image tag) is not same for both deployments.

1. You can also look at the new deployment details and make sure that correct nginx image (**v1.8**) is used.

    `kubectl describe deployment nginx-deployment`  


# Congratulations!

You have successfully completed this lab. To mark the lab as complete, click **End**.
