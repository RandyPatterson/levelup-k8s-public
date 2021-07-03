
![](content/image1.png)  

# Lab: Module 5 - Kubernetes Core Concepts  

>Duration: 90 minutes  

# Table of Contents

[Exercise: Create Azure Kubernetes Service (AKS) Cluster](#exercise-create-azure-kubernetes-service-aks-cluster)  

[Exercise: Creating a Pod Declaratively](#exercise-creating-a-pod-declaratively)  

[Exercise: Creating and Filtering Pods with Labels](#exercise-creating-and-filtering-pods-with-labels)   

[Exercise: Adding/Updating/Deleting Labels on a Pod](#exercise-addingupdatingdeleting-labels-on-a-pod)  

[Exercise: Working with ReplicaSets](#exercise-working-with-replicasets)  

[Exercise: Working with Deployments](#exercise-working-with-deployments)  

[Exercise: Working with Services](#exercise-working-with-services)  

[Exercise: Working with Persistent Volumes Claims & Secrets](#exercise-working-with-persistent-volumes-claims-secrets)  

### Prerequisites 

* WSL 2 Ubuntu 18.04 
* Windows Terminal
* Visual Studio Code 
* Docker Desktop for Windows 

>Use [this](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers) tutorial for installation and setup instructions
 
### Objectives

Lab provide walkthroughs on various Kubernetes topics:  

  - Creating an AKS cluster  

  - Creating, deploying, and deleting Pods  

  - Deployments, Services, Volumes and Secrets  

  - Working with Labels and Annotations  



## Exercise: Create Azure Kubernetes Service (AKS) Cluster  

Open a Linux terminal by launching  **Windows Terminal** and choosing **Ubuntu 18.04**.

![m1image1](./content/m5image1.png)


In this exercise you will create an AKS cluster.  

### Tasks  

**1.  Connecting to your Azure Subrciption with az CLI**  

Login to Azure with the command that will open a Firefox to allow
you to connect with your Microsoft account. Once connected, you can
close Firefox and go back to the command prompt.  
`az login`  

The command line will automatically open your browser to login. Once
you are logged in using your Microsoft account associated with Azure
subscription, close the browser and go back to command line.  

Once logged in, you should see a list of subscriptions once you are
logged in. You can also run the following command to see the list of
subscriptions in the table format.  
`az account list -o table`  

Set the correct subscription, put the name of the subscription in
quotes.  
`az account set --subscription "<subscription_name>"`  

**2.  Creating the AKS cluster**  

Run the following command to deploy Kubernetes cluster with AKS
(replace **INITIALS** with your initials).  
`az aks create --resource-group "USE CLOUD SLICE ASSIGNED RESOURCE GROUP" --name aks-k8s-cluster --node-count 1 --node-vm-size "Standard_DS2_v2" --generate-ssh-keys --vm-set-type VirtualMachineScaleSets --disable-rbac`   

>Note that this command will generate public/private ssh keys.  

>It may take approximately 10 minutes for the cluster to provision
>successfully in the US. If you are in Europe, the provisioning may take
>about 30 minutes.  

Run the following command to download the AKS Kubernetes cluster configuration to the local config file: **$HOME/.kube/config**  
`az aks get-credentials --resource-group k8s-aks-cluster-rg-INITALS --name aks-k8s-cluster`  




## Exercise: Creating a Pod Declaratively  

This Exercise shows the use of YAML file to create a pod declaratively.  

## Tasks  

**1.  Create a Pod declaratively**  

To create a Pod, you will use the YAML file provided to you. You may
want to open the **simple-pod.yaml** file and understand its contents.
The pod definition contains the **Nginx** container to run at port 80
inside the Pod.  
`kubectl apply -f simple-pod.yaml`  

Now, make sure pod is up and running  
`kubectl get pods`  

You should see pod named **nginx-pod**   

**2.  Accessing Nginx container running inside the Pod**  

One of the easiest way to call a pod is to use a port forwarding technique. Run the port-forward command  
`kubectl port-forward nginx-pod 8001:80`  

Browse to the **Nginx** Website. Open the browser and navigate to **http://localhost:8001**  

You should see a page that looks like the following   
![](content/image3.png)   

Once you are done, make sure to stop port forwarding by pressing 
**control-C** key combination. If your host machine is Windows, try **Windows-C**
key combination.  

**3.  Deleting a running Pod**  

You are now going to delete **nginx-pod**. It may take few seconds to
get deleted.  
`kubectl delete pod nginx-pod`  

The Exercise on creating pods declaratively has been completed.  

## Exercise: Creating and Filtering Pods with Labels  

In this Exercise you will create a pod that has labels associated with
it. Labels make it easy to filter the pods later. Labels play vital role
in Kubernetes ecosystem, so it's important to understand their usage
properly.  

Resources needed to complete this exercise are available inside module1
folder.  

## Tasks  

**1.  Create a Pod declaratively**  

The **simple-pod-with-labels.yaml** file contains a pod definition which
is identical to **sample-pod.yaml**, except that it has two labels
assigned to it.  

Run the command to create the pod  
`kubectl apply -f simple-pod-with-labels.yaml`  

Now, make sure pod is up and running  
`kubectl get pods`  

You should see pod named **nginx-pod-with-labels** running.  

**2.  Show all labels that assigned to a pod**  

When you run `kubectl get pods` command, it won't show labels
associated with the pods.  

To get labels related information using the command with
**--show-labels** switch    
`kubectl get pods --show-labels`  

If you want to get labels that are assigned to a pod, then use the
name of pod in the command  
`kubectl get pods nginx-pod-with-labels --show-labels`  

**3.  Filtering pods based on a label**  

Let's say you want to only list pods that have label named
**kind=web** associated with them. You can use **-l** switch to apply
filter based on labels.  
`kubectl get pod -l kind=web`  

To prove that it works as expected run the command again but change
the value of label **kind** to **db**. Notice, this time command won't
return any pods because we don't have any pod with label **kind** with
the value **db**.  
`kubectl get pod -l kind=db`  

## Exercise: Adding/Updating/Deleting Labels on a Pod  

In this Exercise you will create pod that has labels associated with it.
Labels make it easy to filter the pods later. Labels play vital role in
Kubernetes ecosystem, so it's important to understand their usage
properly.  

## Tasks  

**1.  Assigning new label to a running Pod**  

You can assign a new label (key=value) pair to a running pod. This
comes handy when you are troubleshooting an issue and would like to
distinguish between different pod(s). In this case, we will assign a
new label **health=fair** to the pod **nginx-pod-with-labels**, which is
already running.  
`kubectl label pod nginx-pod-with-labels health=fair`  

Now run the command to show the pod labels. Notice now, that an
additional label is added to the pod.  
`kubectl get pods nginx-pod-with-labels --show-labels`  

**2.  Updating value of an existing label that is assigned to a running pod**  

You can also update the value of an existing label that is assigned
to a running pod. Let's change the value of the label **kind=web** to
**kind=db** that is assigned to **nginx-pod-with-labels** pod.  
`kubectl label pod nginx-pod-with-labels kind=db --overwrite`  

**--overwrite** is needed because the pod is running and won't accept changes
otherwise.  

Now run the command to show the pod labels. Notice now, that kind has
changed from **web** to **db**.  
`kubectl get pods nginx-pod-with-labels --show-labels`  

**3.  Deleting label that is assigned to a running Pod**  

Let's delete a label **health** that we assigned earlier to the
**nginx-pod-with-labels** pod  
`kubectl label pod nginx-pod-with-labels health-`  

>Notice the minus (**-**) sign at the end of the command. You can also remove
a label from all running pods by using **--all** switch  
`kubectl label pod health- --all`  

Now run the command to show the pod labels. Notice now, that health is
not part of labels.  
`kubectl get pods nginx-pod-with-labels --show-labels`  

**4.  Deleting the Pod**  
To delete the Pod, run the following command:  
`kubectl delete pod nginx-pod-with-labels`  

## Exercise: Working with ReplicaSets  

In this Exercise, you will create a ReplicaSet, which will ensure that
there are always specified number of Pods running in the cluster. Label
selectors are used in the ReplicaSet as a criterion to match the pods.
Any time the total number of matching Pods falls below the specified
number defined in the ReplicaSet definition, the ReplicaSet controller
will make sure it brings the actual number of Pods to the specified
value.  

A ReplicaSet may create/delete Pods any time it sees the total number of
Pods fall below or above the specified value. In doing so, it may
terminate or create new Pods but it never relocates or migrates them.  

## Tasks  

**1.  Creating Pods through ReplicaSet**  

The **ng-rc.yaml** file contains a ReplicaSet definition that contains
Pod definitions, as well as a count for the number of Pods that need
to be running at any given point in time. You may want to open the
YAML file and notice the matchLabel field that contains **key=value**
for matching the Pods labels.  

Run the command to create a ReplicaSet.  
`kubectl apply -f ng-rc.yaml`  

Now, run the following command to see two new Pods created by the
ReplicaSet  
`kubectl get pods --show-labels`  

**2.  Testing the ReplicaSet Controller**  

With the Pods up and running, you will check if the ReplicaSet
controller is working as expected. You will do that by removing the
label (**target**) from one of Pods created by the ReplicaSet. This
should immediately make ReplicaSet spin up a new Pod since
**matchLabels** criteria demands a minimum two Pods with the label
(**target=dev**).  

First remove the label (you should have listing of Pods along with
their names from pervious step)  
`kubectl label pod <POD-NAME> target-`  

Now, run the following command to see one new Pod created by the
ReplicaSet, while an old Pod is still running.  
`kubectl get pods --show-labels`  

You can delete the additional Pod manually, but a better way to do
that is by assigning the (**target=dev**) label back to the Pod. This
will make the ReplicaSet terminate one of the Pods to ensure that
the total Pod count remains as two (and not three).  
`kubectl label pod <POD-NAME> target=dev`    

**3.  Deleting the ReplicaSet**  

To delete the ReplicaSet, run the following command:  
`kubectl delete rs nginx-replica-set`  

This will delete not only the ReplicaSet, but also all the Pods that
it was controlling by using the matching label criteria.  

**4.  Deleting the remaining Pod**

To delete the Pod, that we excluded from the ReplicaSet when we
removed the target label, run the following command:  
`kubectl delete pod <POD-NAME>`  

## Exercise: Working with Deployments

In this Exercise you will create a Deployment and roll out an
application update. Deployments provide a consistent mechanism for you
to upgrade to a new version of applications while making sure there is
no or very little downtime. Please note that internally, Deployments use
ReplicaSets for managing Pods. However, you don't need to work directly
with ReplicaSets, since Deployments abstract out that interaction.  

## Tasks  

**1.  Creating a new Deployment**  

The **ng-dep.yaml** file contains a Deployment definition. If you
notice, this is nearly identical to the ReplicaSet definition that
you used before. The Pod is going to create a nginx container with a
tag **1.0**. The **1.0** represents the version number of this container and
hence the application running inside it.   

Run this command to create a Deployment and associated ReplicaSet.  
`kubectl apply -f ng-dep.yaml`  

Now, run the following command to see new the Pods that are created.  
`kubectl get pods --show-labels`  
![](content/image10.png)   
  
**2.  Accessing the 1.0 version of application**  

You will now browse to the nginx container by using the **port-forward**
command. First note down the pod name as displayed by the output
from the previous command. Just choose one of the pod's names, it's
not important which one you choose.  
`kubectl port-forward <POD-NAME> 8001:80`  

Open the browser and navigate to **http://localhost:8001**    
![](content/image11.png)  
  
**3.  Updating the Deployment with 2.0 version**  

You are now going to update the deployment to run **2.0** version of
containers instead of **1.0**. This can be done in two ways. The
approach taken in this task is going to show you the imperative way
of doing it, which is faster and will help you shorten your time
during dev/test of your application. The alternate approach is what
you have seen so far where you will use a YAML file.  

Make sure to stop the port forwarding before proceeding further. You
can use **Windows-C** or **Command-C** key combination to terminate it.  

To start rolling out the new update, you will tell the deployment to
change the container image tag from **1.0** to **2.0** by running the
command:  
`kubectl set image deployment ng-dep nginx=k8slab/nginx:2.0`  

In the command above, **ng-dep** is the name of deployment and **nginx** is the
name assigned to the container within the Pod template. Since the
original version of the deployment was using **1.0** tag to pull down the
container image, the above command will force the deployment to roll out
a new deployment with image tagged as **2.0**  

Now, list all the pods and notice that old pods are terminating, and the
new pods have been created.    
`kubectl get pods`  

Take a note of one of the newly created pods (the one created most
recently).  

If you want to look at the deployment definition with the updated value
of container image, run the following command:  
`kubectl describe deployment ng-dep`  
![](content/image12.png)  
>Notice the Image section (under Containers) with the value of container
>image being updated to **2.0**.

**4.  Accessing the 2.0 version of application**  

You will now browse to the **nginx** container by using the **port-forward**
command. Replace the pod name by the one you noted in the last **get pods** command.    
`kubectl port-forward <POD-NAME> 8001:80`  

Open the browser and navigate to http://localhost:8001  

![](content/image13.png)  

Make sure to stop the port forwarding before proceeding further. You
can use **Windows-C** or **Command-C** key combination to terminate it.   

**4.  Deleting the deployment**   
Finally, remove the deployment with the following command  
`kubectl delete deployment ng-dep`   

## Exercise: Working with Services  

In this Exercise you will create a simple Service. Until now, you have
been using the **port-forward** command to gain access to the Pods.
Although, this works fine during dev/test, this approach does not really
scale. Services help you expose Pods externally using label selectors.  

## Tasks  

**1.  Creating a new Service**  

The **ng-svc.yaml** file contains a Services definition. Services use
label selectors to determine which Pod it needs to track and forward
the traffic.  

Let's start by taking at a look at running Pods and their labels.  
`kubectl get pods --show-labels`  

Notice the label **target=dev** that is associated with the pods.  

Now, open the **ng-svc.yaml** file and examine the **selector** attribute
that is followed by **target=dev**. This essentially means that this
Service will track all Pods that have label **target=dev** and forward
traffic to them.  

Run the following command to create the Service:  
`kubectl apply -f ng-svc.yaml`  

Now, check the of newly created service:  
`kubectl get svc -o wide`  

The above command will display the details of all available services
along with the label selectors. You should see in the output, **ng-svc**
with **PORTS 80:30101/TCP** and **SELECTOR target=dev**.  

**2.  Accessing the ng-svc Service**  

Open a browser and navigate to that IP address.  

**3.  Deleting Deployment and Service**  

You are now going to delete Pods that were created earlier by
deleting the Deployment. Also, you will delete the Service.  

To delete the Deployment run the command:  
`kubectl delete deployment ng-dep`  

To delete the Service run the command:  
`kubectl delete service ng-svc`  

## Exercise: Working with Persistent Volumes Claims & Secrets  

This Exercise shows the use of secrets to hide the password needed by
WordPress to connect with MySQL. You will first create the secret
declaratively using YAML file and then use it within the deployment
files. Also, labels are used to ensure pods are tagged properly based on
their usage. For example, WordPress pod is assigned **tier=frontend** value.    

## Tasks  

**1.  Create a secret declaratively**  

First you will create a new secret by using the **apply** command and
passing the secret definition as an YAML file. You may want to open
the YAML file in Visual Studio Code and look at the data value which
is encoded in base64. This is required by Kubernetes.    
`kubectl apply -f mysql-secret.yaml`  

**2.  Deploy MySQL Pod (Single YAML file contains Deployment, Service and Volume Claims definitions)**  
`kubectl apply -f mysql-pvc-dep-svc.yaml`  

This may take few minutes. Run `kubectl get pods` commands to make sure
the pod is running before proceeding further.  

**3.  Deploy WordPress Pod (Single YAML file contains Deployment, Service and Volume Claims definitions)**   
`kubectl apply -f wordpress-dep-svc-pvc.yaml`  

Capture URL to access WordPress service.  

Run the following command until the external IP goes from pending to
an IP address.  
`kubectl get svc`  

![](content/image8.png)  

**4. Browse to the WordPress Website**  

Open a browser and navigate to **http://<external-ip>** you should see
WordPress  

![](content/image9.png)  

**5.  Deleting Deployments and Services**  

You are now going to delete Pods that were created earlier by
deleting the Deployments. Also, you will delete the Services.  

To delete the Deployment run the command:  
`kubectl delete deployment wordpress`  
`kubectl delete deployment wordpress-mysql`  

To delete the Service run the command:  
`kubectl delete svc wordpress`  
`kubectl delete svc wordpress-mysql`  

# Congratulations you have completed  Lab 1, click **Next** to continue to Lab: Module 2 - Kubernetes Application Development on Azure  

===

![](content_m2/image1.png)  

# Lab: Module 2 - Kubernetes Application Development on Azure  

>Duration: 90 minutes  

# Table of Contents

[Exercise: Working with Helm Charts](#exercise-working-with-helm-charts)

[Exercise: Working with Visual Studio Code](#exercise-working-with-visual-studio-code)

[Exercise: Debug remotely a .NET Core application using Azure Dev Spaces](#exercise-debug-remotely-a-.net-core-application-using-azure-dev-spaces)

[Exercise: Working with Init Containers](#exercise-working-with-init-containers)

[Exercise: Working with PostStart Container Hooks](#exercise-working-with-poststart-container-hooks)

[Exercise: Using ACI Connector with Azure Kubernetes Service (AKS)](#exercise-using-aci-connector-with-azure-kubernetes-service-aks)

**Objectives**  

Lab provides exposure on various Kubernetes topics:  

  - Creating and deploying Kubernetes multi-container application using
Helm Charts  

  - Using Visual Studio Code to deploy Docker containers in AKS  

  - Debugging remotely with Azure Dev Spaces  

  - Creating and using InitContainers  

  - Creating and using PostStart container hooks  

  - Using ACI-Connector with Azure Kubernetes Service  

All the Labs will require an AKS cluster to be up and running. Make sure you are connected to the AKS cluster with the following command to run in a terminal:  

`kubectl config current-context`  

It should return **aks-k8s-cluster**. If that is not the case, run:  

`az aks get-credentials --resource-group k8s-aks-cluster-rg-<INITALS> --name aks-k8s-cluster`  

## Exercise: Working with Helm Charts 

In this exercise you will use the Helm charts to deploy a
multi-container application. Helm is powerful tool that helps you manage
Kubernetes applications. Helm Charts are core components of Helm that
helps you define, install, and upgrade Kubernetes application.

The multi-container application consists of a front-end ASP .NET Core web
application and a Redis back-end. Overall, the application itself is
very simple but helps you understand all the basic components of Helm.

## Tasks

**1.  Understanding Helm Charts Structure**  

Helm charts follow a folder structure pattern. Typically, it looks like following  
    
    /parent-application    
    chart.yaml   
    /templates   
    /charts  
    /charts/application-1  
    /charts/application-1/templates  
       
In our case we have two applications. From the Helm charts
perspective, we have defined **front-end** as parent application and **back-end** application sits below it.    
Lets' view the folder structure for **voting-application** that
contains Helm Charts for both **front-end** and **back-end** application.
Run the following commands:  
`cd ~/Labs/Module2`  
`tree -d ./voting-app-helm`  
![](content_m2/image2.png)  

Notice that you have **templates** folder right underneath the main
application folder and then another **charts** which also has another
**templates** folder. Since we have two applications, **front-end** and
**back-end**, we want to segregate the YAML files into two separate
folders resulting in two charts. These charts are related because **front-end** and **back-end** are within the same root.  
Let's explore more closely the YAML files within these folders. This
will give you better understanding on how various files are placed
within this hierarchy.

`tree ./voting-app-helm`  

![](content_m2/image3.png)  
>Helm Charts directory structure for complex applications can grow quite
>deep. Since we are just starting to work with Helm, keeping things
>simple make sense. However, you should look at the mature product like
>OpenStack and its entire Helm chart structure available at
><https://github.com/sapcc/helm-charts/tree/master/openstack>. This
>should give you a good idea on how Helm Charts are structure for more
>advanced applications.  
>Notice that inside the OpenStack Helm folder structure, how all the
>subcomponents are divided into sub charts (via sub folders). For
>example, Elektra is Web UI with a PostgreSQL backend sits level below
>within the main charts folder -
><https://github.com/sapcc/helm-charts/tree/master/openstack/elektra>  

Let's examine these files closely. **Chart.yaml** is Helm specific file
that contains basic set of information or metadata about Helm
application package. This include application version number etc. Notice
that there are two different **Chart.yaml** files, each file corresponds
to one of the application that we are planning to deploy (i.e.,
front-end and back-end).  
   
Review the content of **Chart.yaml** file for the **front-end**
application by running following command.  
`cd voting-app-helm`  
`cat Chart.yaml`  

![](content_m2/image4.png)   

However, chart files do not contain application specific details
including image name, deployment, services etc. For that, you use
**values.yaml** file, which act like a centralized location to define
application specific details for all the Kubernetes artifacts.  

Let's view the contents of **values.yaml** for the front-end
application.  
`cat values.yaml`  

![](content_m2/image5.png)  

Notice we have name, **replicaCount** etc. These are the values that we
will reference within the Deployment and Service definition files for
the **front-end** application.  

Let's view the content of **voting-frontend-dep.yaml** located in the
templates folder.  

![](content_m2/image6.png)  

The deployment file for the **front-end** application is not much
different from the type deployment file you worked with before using
Helm, but it does contain placeholders for certain fields. These
placeholders will read the values from **values.yaml** file and replace
the placeholder with that value. This way you just focus on defining the
structure and leave most of the specifics to be defined in values file.
However, you can always define specific values and not read them from
values file. In this case notice that the values of CPU limits and
requests in the deployment file are hardcoded and are not read from
values file.  

At any point if you like to make sure that the Helm CLI and Tiller
(server side component which runs as a Pod) are working correctly run
the following command:   
``helm version``  

![](content_m2/image7.png)  

As it turns out, we have not initialized Helm in the cluster, so we are
seeing an error saying that Tiller is not found. To properly initialize
the cluster, we need to run the command  
`helm init`  

The cluster will need a few seconds to start the Tiller pod. Then, it
should show you both client and server version of helm with the command  
`helm version`  

![](content_m2/image8.png)  

**2.  Verifying a Helm Chart**

Before you deploy the chart, you can optionally verify it. This
helps in making sure that everything is defined correctly, and all
values are exactly what you want them to be.  
`cd ~/Labs/Module2`  

To verify the helm chart run following command:  
`helm template ./voting-app-helm`  

The output of this command will display all the YAML files within
templates folder (for both **front-end** and **back-end**). Notice
that the output contains the actual values for all the templates
files after being replaced with the values from **values.yaml**
file. These are the final values and if you found something missing
or a discrepancy then you may want to change the **values.yaml**
file as needed.   

**3.  Installing a Helm Chart**  

Installing a Helm chart means that you will ask helm to connect to
Tiller pod. It does that automatically when you run the following
command. This will install the **front-end** and **back-end**
applications through Helm chart. Notice you don't have to run
`kubectl apply` command since there Helm will take care of installing
all the relevant files since you already define them into template
folder earlier. Also, **--name** parameter is used to give unique name
to this installation. In Helm, you refer the installed application
with a release name.  
`helm install ./voting-app-helm --name voting-app`  

Please note the installation may take few minutes.  

![](content_m2/image9.png)  

You can always dry run the installation and see any debug messages by
running following command. This will not install the chart but still
allows you dry run the installation.  
`helm install ./voting-app-helm --dry-run --debug`  

**4. Listing a Helm Chart**  

You can list all the releases by running following command.  
`helm ls`  

Output will provide you with details including release name, status,
chart details, app version etc.  

**5.  Accessing Voting Application**  

Once deployed, you can run the following command to see the external
IP created as part of the Load Balancer service.  
`kubectl get svc`  

![](content_m2/image10.png)  

Open the browser and navigate to **http://\<external\_ip\>**. Once the
application home page is displayed you should able to vote.  

![](content_m2/image11.png)  

**6.  Deleting a Helm Chart**   

When you no longer need the application, you can delete the Helm
chart. Remember we have installed multi-container application using
single command earlier, and now remove it again with simple
commands. Not need to run mulitple `kubectl delete` commands.  
`helm delete --purge voting-app`  

Helm delete command without the **--purge** does the soft delete of the
release. You can still access the release by running `helm ls --all`
command which will show the release status as **"DELETED"**. However, purge
command will completely remove the release.  

## Exercise: Working with Visual Studio Code  

In this exercise, you will learn how Visual Studio Code can help when working
with Kubernetes clusters. Visual Studio Code provides a consistent user
experience across Windows, MAC OS and Linux operating systems.  

## Tasks

**1.  Creating Azure Container Registry**  

To share container images, private registries can be used like
[Azure Container
Registry](https://docs.microsoft.com/en-us/azure/container-registry/).
As part of this exercise, we will be using a private registry hosted
in Azure.  

First open VS Code and open the Command Palette as shown below (or
with **control-shift-p**).  

![](content_m2/image57.png)  

![](content_m2/image12.png)  

Enter **Azure sign in** the text box and select **Azure sign in**. It will open
a browser and walk you through the sign in experience to authenticate
the IDE.  

![](content_m2/image13.png)  

A pop up will appear prompting you to enter the Admin password
**P@ssw0rd123\!**  

Open the Command Palette again and enter **Create** in the text box. Then
select **Docker: Create Azure Registry**.  

![](content_m2/image14.png)  

You will need to choose your subscription for billing purpose (select
**Azure Pass** subscription). For the resource group, click on **create new resource group** and select the location closest to you.  

Then, you will need to select the Sku (keep **Standard**) enter the Azure
Container Registry name (it must be unique across Azure) and location.
At the end of these steps, you should see a notification saying that it
has been successfully created (at the bottom right).  

To see the resource we have just deployed, navigate to
[portal.azure.com](https://portal.azure.com/) and click on **All Services**.   

![](content_m2/image15.png)  

Then enter **Container registries** and click on **Container registries**.    

![](content_m2/image16.png)  

Finally, click on the registry we just created to access the **Overview**
page.  

![](content_m2/image17.png)  

We have deployed a private Docker Container registry in Azure from
Visual Studio Code.  

**2.  Connecting VS Code to ACR account and Kubernetes cluster**   

Now that we have successfully deployed the ACR, we need to
authenticate VS Code to this private registry. To do this, we need
to browse to our Container Registry as explained in the previous
section and go to **Access keys** to find the credentials as shown
below.  

Enable **Admin user** and take note of the **Login server**, **Username** and
**Password**.  

![](content_m2/image18.png)  

From VS Code, open the **Settings** at the bottom left as shown below.  

![](content_m2/image19.png)  

Search for **vsdocker** and override the **Vsdocker: Image User** with
**<login_server>/<user_name>**.   

> ![](content_m2/image20.png)  

At the top of VS Code, click on **Terminal** - **New Terminal**  

![](content_m2/image21.png)  

In the terminal that has been opened, enter the command `docker login <login_server> -u <user_name>` as well as the password found in the
ACR environment.  

![](content_m2/image22.png)  

Before we can deploy a basic application to a Kubernetes cluster, we
need to connect to the right cluster. Type the following commands from
the VS Code terminal  
`az login`  
`az account set --subscription "<subscription_name>"`  
`az aks get-credentials --resource-group k8s-aks-cluster-rg-<INITALS> --name aks-k8s-cluster`  
`kubectl config current-context`  

It should return `aks-k8s-cluster`. If that is not the case, run:  
`kubectl config set-context aks-k8s-cluster`   

**3.  Deploy NodeJS application to Kubernetes from VS Code**    

From VS Code, click on **Explorer** and then **Open Folder**.  

![](content_m2/image23.png)  

Select the folder **~/labs/Module2/nodejs** and click **OK**. You can see
the basic Node JS application provided, as well as a **Dockerfile** to
build its image.  

![](content_m2/image24.png)  

Open Command Palette by clicking on the **Settings** icon and then
**Command Palette**  

![](content_m2/image25.png)  
 
Type **Kubernetes Run** and select it. It will build the Node JS image,
push it to Azure Container Registry and deploy the application into
your AKS cluster.

![](content_m2/image26.png)

You can follow these steps at the bottom left of the IDE. First building.  

![](content_m2/image27.png)  

And once built, pushing to the ACR.  

![](content_m2/image28.png)  

And finally, the deployment should be created.  

![](content_m2/image29.png)  

Once complete, click on the Kubernetes extension, then expand the
cluster and click **Workloads** - **Deployments** - **nodejs**. You will be able
to see what is deployed in your cluster.    

![](content_m2/image58.png)  
  

**4.  Troubleshooting and fixing a failing deployment**    

When expanding **nodejs**, you can see a red dot meaning that things did
not go as expected.  

Right-click on the failing pod and click **Describe**. Scroll down and
locate the message **Failed to pull image ...**, as highlighted in red
below. If you scroll to the right, you will see that the pull failed
because the AKS cluster is unauthorized to pull the image stored in
the Azure Container Registry. We need to create a secret.  

![](content_m2/image30.png)  

To create a secret, open the VS Code Terminal and type the command  

``` bash
kubectl create secret docker-registry acr-auth  
    --docker-server=https://<acr_login_server>  
    --docker-username=<acr_user_name>  
    --docker-password=<acr_password>  
    --docker-email=<any_mail>
```   

![](content_m2/image31.png)  

Although the secret is created, we need to make sure that its
credentials will be used to download the image at deployment time.  

From the Kubernetes extension window, click on the **nodejs** deployment
to see the YAML file describing the deployment.  

![](content_m2/image32.png)  

After the **containers** section, update the file to add the following YAML section:  
``` yaml
imagePullSecrets:
  - name: acr-auth
```     

![](content_m2/image33.png)  

Finally, to update the deployment, open the Command Palette (or
**control-shift-p**), search for **Apply** and select **Kubernetes: Apply**
and click **Apply**.  

![](content_m2/image34.png)  

Now if you refresh the cluster's view, the **nodejs** pod should be healthy once the pull is complete. It will take a bit of time. Feel free to right click on the pod and **Describe**, it should show the event **pulling image...**   

![](content_m2/image35.png)   

**5.  Exposing the deployment publicly with services**    

From the cluster's view, if you look under **Services**, you will see
that the Node JS application is deployed but is not exposed through
a service. To do this, open the Terminal and run  
`kubectl expose deployments/nodejs --port=80 --target-port=8080 --type=LoadBalancer`  

![](content_m2/image36.png)  

Note the target port, which is 8080 because, as specified on the
**Dockerfile** that we used to build the image, we expect traffic to
come through the port 8080 in the container.  

Just like we did from the Linux terminal, we can follow the service
creation from the VS Code terminal with the command (we can also do
that by right clicking on the **nodejs** service created under **Services**
and click **Get**):  
`kubectl get svc`  

![](content_m2/image37.png)   

Once the service has been set up and the IP address has been created
in Azure, you can refresh the cluster view and call the IP address
from any browser.    

![](content_m2/image38.png)  

**6.  Exploring more VS Code features**     

Another convenient integrated tool part of the Kubernetes extension
is the `kubectl explain` command. Open a YAML file by clicking on **nodejs** deployment. Then open the Command Palette and enter
**explain**. Then select **Kubernetes: Explain**.  

![](content_m2/image39.png)  

Now, hover over any field in the deployment YAML file to see some
documentation and clickable links to learn more about the field.  

![](content_m2/image40.png)  

Finally, what we can do is open an interactive session from within the
container for troubleshooting purpose. Right-click on the **nodejs** pod and
click **Terminal**.  

![](content_m2/image41.png)  

You can type the `ls` or `cat server.js` commands to see what is inside the
container file system. All of this, without leaving the VS Code IDE.  

![](content_m2/image42.png)  

**7.  Cleaning the Service and Deployment**    

From VS Code, we can also delete Kubernetes resources. Open the
Command Palette, search for **Delete** and select **Kubernetes: Delete**.
Type **service** and hit **enter**, this will delete the **nodejs** service.  

Start **Kubernetes: Delete** again, but this type **deployment** so that it
deletes the **nodejs** deployment and clean everything up for the next
exercise. You can refresh the cluster's view to check that **nodejs** is
not under Deployments, Pods or Services.  


## Exercise: Debug remotely a .NET Core application using Azure Dev Spaces  

In this exercise you will use Azure Dev Spaces to remote debug a .NET
Core application running in AKS. Azure Dev Spaces can be used in command
line, from Visual Studio or from Visual Studio Code. This tool is only
compatible with AKS and cannot be used on other Kubernetes clusters.   

## Tasks

**1.  Set up Dev Spaces on your AKS cluster**  

In a new terminal, connected as a regular user (**super**), run the
following command to install Azure Dev Spaces CLI and set up Azure
Dev Spaces Controller in the AKS cluster. Select yes when prompted
and set default as a namespace name for the Dev Space.  
`az aks use-dev-spaces -g k8s-aks-cluster-rg-INITALS -n aks-k8s-cluster`  

This command might take few minutes and will require some
confirmations.  

**2.  Create a .NET Core project and remote debug using Azure Dev Spaces**  

Run the following commands in a terminal connected a super user to
create a new .Net Core MVC website  
```sh
mkdir devSpaces
cd devSpaces
dotnet new mvc
```  
Now we will prepare the needed files to deploy this application in
the cluster (**Dockerfile**, Helm chart, ) with the following command  
``azds prep --public``  

Open VS Code and open the folder where the .Net Core application has been created.  You will see a pop up saying that required assets need to be download to properly build this projct. Click **Yes** and wait a few seconds until everything is downloaded.   

![](content_m2/image62.png)  

Click on **Settings** at the bottom Left and then **Command Palette**.   

![](content_m2/image43.png)  

Search for **Azure Dev Spaces** and select **Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces**.   

![](content_m2/image44.png)  

Go to the **Debug** section and click **Debug**  

![](content_m2/image45.png)  

![](content_m2/image46.png)  

You need to ensure that **.NET Core Launch (AZDS)** is selected. By default,
it may not be selected.  

This will synchronize your source files, build the container image and
install the Helm chart in the remote cluster. It will take few minutes
since the **microsoft/dotnet** image has to be downloaded in the AKS
cluster. Once the container has been spun up, you should see a **local url**
allowing you to call the .Net Core website. To find it, go to the
**Terminal** window as shown below.  

![](content_m2/image47.png)  

Go into the **Explorer** section of VS Code and add a breakpoint to the
**Contact** method of the **Home controller**.  
![](content_m2/image48.png)  

Go back to the website and click on **Contact**. You should hit your
breakpoint and experiment the remote debugging experience. Click
continue to display the **Contact** page.  

![](content_m2/image59.png)  

Update the controller. For instances change **"Your contact page"** to **"Your updated contact page"**. Save the file.  

Restart the debugging of the application to synchronize your change with the remote cluster.  
![](content_m2/image60.png)  

After a few seconds, the debugger will restart and you will be able to see the new version of your controller after you refresh the webpage in your browser.  
![](content_m2/image61.png)  

**Now you have a method for rapidly iterating on code and debugging directly in Kubernetes!**  


## Exercise: Working with Init Containers  

## Tasks  

**1.  Creating the Init Container**  

First you will deploy the Pod that has both regular/application
container and the init container defined.  

Make sure you are in a terminal as super user and navigate to the
**Labs/Module2**  
`cd ~/Labs/Module2`  
![](content_m2/image49.png)

You should open **init.yaml** and review its contents.  
`cat init.yaml`  

Notice the use of **initContainers** section with the command the
checks if **nginx** web server is accessible. Until it's not, init
container will make sure that application container, which is busy
box container, won't run.  
`kubectl apply -f init.yaml`  
  

**2.  Checking the Init Container**  

You should now check the status of the Pod.  
`kubectl get pods`  

![](content_m2/image50.png)  

Notice that the status of **myapp-pod** shows **Init:0/1**. This means
that 0 out of 1 init containers are still running. Until init
container's condition is satisfied the application pod won't run.  

You can check what's going on inside the Pod by running the command:  
`kubectl describe pod myapp-pod`  

Scroll down to the conditions section within the output.  

![](content_m2/image51.png)  

Notice that the **myapp-container** is not initialized yet. This is
exactly what you expect because init container is still running.
Lets' look at the logs to verify that.  
`kubectl logs myapp-pod -c init-ngservice`  

You should see "**Waiting for ngservice to come up**" message in the
logs (ignore "**sh: 200: unknown operand**" message as it's not
relevant).  

**3.  Running Init Container to Completion**  

To make sure that init container condition has met, run the
following command. It's going to create a **nginx** service and a
deployment. The service endpoint, once up and running, will satisfy
the init container condition since it will able to connect to it and
get status 200 in return  
`kubectl apply -f myservice.yaml`  

You can take a look at the service file with the command.   
`cat myservice.yaml`

After a couple of minutes, run the following command  
`kubectl get pods`  

![](content_m2/image52.png)  

Notice that **myapp-pod** is now showing **Running** status (previously
its **Init:0/1**), This means init container has completed its job and
is terminated.  

Now, check the logs for application container  
`kubectl logs myapp-pod`  

Notice the message in the logs "**The app is running".** You can
also run describe command to gather more details about the Pod.  
`kubectl describe pod myapp-pod`  

![](content_m2/image53.png)  

**4.  Deleting Resources**  

Make sure to delete relevant resources before finishing this exercise.  
`kubectl delete svc ngservice`  
`kubectl delete pod myapp-pod ng-pod`  
  

## Exercise: Working with PostStart Container Hooks   

PostStart hook gets executed immediately after the container's main
process is started. Typically, you use PostStart hook to perform extra
processing when the application starts. It especially comes handy when
you don't have application source code (third party applications) yet
still want to execute some logic as container gets started.  

Please note that hook runs in parallel (asynchronously) with the main
process, despites what its name suggest. So, don't assume that the main
process will hold for the post start hook to complete. However, if post
start hook fails (return a non-zero exit code), the main container will
be killed.  

## Tasks  

**1.  Creating a Pod with a Post Start Container Hook**  

The **hooks.yaml** file contains the Pod definition along with the
command that will run as PostStart container hook executes.  

Run the following command, to create a Pod with liveness probe.  
`kubectl apply -f hooks.yaml`  

Wait for 5 seconds and then run the command to view Pod details.  
`kubectl describe pods hooks`  

Scroll down and locate the **Events** section. The PostStart hook first
echoed a message, then slept for 3 seconds and finally return an
exit code -1. Since the exit code is non-zero, container was killed
and re-created again. Initially, you will see **PostStartHookError**
within the events section. However, as PostHook will run again and
once again returns exist code -1, the container will be killed again and re-created.  
Take a look at the yaml file we deployed to understand how the hoo was specified and defined
`cat hooks.yaml`  

**2.  Deleting the Pod**  

Make sure to delete the Pod before closing this exercise  
`kubectl delete pod hooks`  
  

## Exercise: Using ACI Connector with Azure Kubernetes Service (AKS)

In this exercise you will learn how to leverage Azure Container
Instances (ACI) to host environment for running containers in Azure. You
will run Windows Nano Server container with IIS webserver. When using
ACI, there is no need to manage the underlying compute infrastructure,
Azure handles this management for you. When running containers in ACI,
you are charged by the second for each running container.  

With the Virtual Kubelet provider for Azure Container Instances, both
Linux and Windows containers can be scheduled on a container instance as
if it is a standard Kubernetes node. This configuration allows you to
take advantage of both the capabilities of Kubernetes and the management
value and cost benefit of container instances. This is  recommended for burtable workloads to extend the computing power of Kubernetes clusters.   

This exercise has following prerequisites:  

  - AKS cluster up and running.  

  - You also need the Azure CLI version 2.0.33 or later. Run `az --version` to find the version.    

  - To install the Virtual Kubelet, Helm is also required. Run the
command `helm ls` to make sure helm client and server and working as
expected. If you ran into issues, then first delete the tiller pod
on the server and then reset the helm.  
`kubectl delete deployment tiller-deploy --namespace kube-system`  
`helm init`  

## Tasks  

**1.  Installing ACI-Connector**    

You will need the resource group name and AKS cluster name that you
created in the previously. You can get these values easily by
running the following command. Note down the name of resource group
and the name of AKS cluster.  
`az aks list -o table`  

To install **aci-connector** you will run the following command. Pass in
the values for **resource-group** and **name** parameters. In this
case you are going to run Windows container hence the **os-type** is
set to windows.  
`az aks install-connector --resource-group k8s-aks-cluster-rg-<INITALS> --name aks-k8s-cluster --connector-name virtual-kubelet --os-type windows`  

![](content_m2/image54.png)  

Now run the command to list all the nodes in the cluster. You should
see **virtual-kubelet** node shows up among the list of available nodes.
Note down the name of the virtual kubelet node.  
`kubectl get nodes`  

![](content_m2/image55.png)  

**2.  Running a Windows Nano Server Container**  

You have provided with the deployment file, **virtual-kubelet-windows.yaml**. Open and examine the deployment file.
Notice that this file schedule a Pod on an ACI node and runs
**nanoserver-iis** container with IIS webserver listening on port 80.
Also, to ensure only the ACI node runs the Pod that runs windows
container, nodeSelector and toleration are used.  

Make sure that the **nodeSelector** field has the correct value assigned to
the **kubernetes.io/hostname** field. It should match the name of your ACI
node. You have noted it down in the previous task. If they don't match
make sure to update the value in the YAML file before proceeding.  
`kubectl create -f virtual-kubelet-windows.yaml`  

After few seconds run the command to list the **nanoserver-iis** Pod .  
`kubectl get pods -o wide -l app=nanoserver-iis`  

Notice that the nanoserver based Pod has been scheduled but not ready.
It may take few minutes for it to be ready. Meanwhile you can look at
the YAML file we just deployed, as well as the various messages that
will help you understand Pod assignment to the ACI node.  
`kubectl get pods -o wide app=nanoserver-iis`  

After some time, you will have a Windows container running in a pod as
part of the ACI environment.  

![](content_m2/image56.png)  

