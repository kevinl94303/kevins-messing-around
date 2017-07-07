# Rolling Upgrade in Rancher 1.6
When we want to update a global service, the built-in "rolling upgrade" scheme is not available. (After reading online, I believe it is because Rancher's default "rolling upgrade" creates a new service to replace the old one, and the public ports are already occupied by the old service). However, Rancher supports an "in-service upgrade" which can be configured to perform a rolling upgrade in a desired way. 

Be aware that this method requires two or more instances of the container running. If you only have one instance of the desired container running, you can either:

1. Scale up the service to two or more replicas. This method creates multiple instances of the container in one host, which can then be updated using the method of your choice

2. Run another instance of the desired container on another currently running host. This may cause problems if the container you want to upgrade has conficts with another container running on the host. 

3. Start up another host to run another instance of the container. This is the safest method, at the cost of temporarily using extra computing power to run another host. 

# In the Rancher Web UI
(Source: http://rancher.com/docs/rancher/v1.6/en/cattle/upgrading/#in-service-upgrade)

In the Rancher Web UI, select the targetted stack which contains the service to upgrade

Select the service to upgrade

In the upper right-hand corner, there is an "upgrade" button shaped like ⬆️. Select this button.

For "Batch Size", enter "1". This will cause one container to be upgraded at a time. For "Batch Interval", set 60 seconds. This will ensure there is enough time for the election process and data transfer between old and new containers. Make sure to leave "Start Behavior" unchecked to preserve container data (reason is explained in the next section)

Enter the desired address to pull the image from

Scroll down, and if desired, click the "Health Check" tab, and set to something appropriate

It may also be convenient to click on "Networking", and check the box next to "Reuse IP addresses when upgrading or replacing unhealthy instances."

Click "Upgrade"

Once the containers have finished updating, click the ✔️ button in the top right-hand corner to confirm the upgrade and remove the old containers

# In the Terminal using Rancher-Compose

Note: For some reason, updating using the command line breaks the UI updating feature. I will further investigate this. Because of this and other bugs, updating through rancher-compose is undesirable at the moment. However, it does offer more flexibility in editing the docker-compose.yml file. 

Following the steps from 
http://rancher.com/docs/rancher/v1.6/en/cattle/rancher-compose/, install rancher-compose from the Rancher Web UI, scrolling down to the page footer and selecting "Download CLI > Rancher Compose"

In the Rancher UI navbar, click on "API > Keys"

Click "Advanced Options"

Click "Add Environment API Key". Name the Key whatever you want. Once the Key is generated, copy+paste this into a secure place for future use. 

In the command line, run: 
```
#This is the URL of the Rancher UI
export RANCHER_URL=<http://server_ip:port_number/>
export RANCHER_ACCESS_KEY=<username_of_environment_api_key>
export RANCHER_SECRET_KEY=<password_of_environment_api_key>
```
using the Key you just generated

Navigate to the folder in which you have a docker-compose.yml file prepared. Make sure the name of the folder is the same as the stack you are updating. E.g. if the name of the stack is "Neuvector", the folder should be named "Neuvector". Otherwise rancher-compose will create a new stack with the name of the folder. 

Note that Rancher offers the option of adding a rancher-compose.yml to set it so that new containers are created before old containers are stopped. However, testing has indicated that this causes previous container data to be overwritten. This is likely caused by undesirable results in the election process: when the first container is being updated, it is removed from the network before it is stopped (to create room for the new container). Thus, we should not use this option. 

Run: 
```
rancher-compose up -u --interval "60000" --batch-size "1"
```
If the image has changed but the tag has not, you can use the -p option to repull the image. 

Once the containers have finished updating, you can use
```
rancher-compose up -u -c
```
to confirm the upgrade and delete the old containers
