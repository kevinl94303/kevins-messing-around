# Rolling Update in Rancher 1.6
# In the Web UI
(Source: http://rancher.com/docs/rancher/v1.6/en/cattle/upgrading/#in-service-upgrade)

In the Rancher Web UI, select the targetted stack which contains the service to upgrade

Select the service to upgrade

In the upper right-hand corner, there is an "upgrade" button shaped like ⬆️. Select this button.

For "Batch Size", enter "1". This will cause one container to be upgraded at a time. For "Batch Interval", set 30 seconds. This will ensure there is enough time for data transfer between old and new containers. Leave "Start Behavior" unchecked. 

Enter the desired URL to pull the image from

Scroll down, and if desired, set the "Health Check" to something appropriate

Click "Upgrade"

Once the containers have finished updating, click the ✔️ button in the top right-hand corner to confirm the upgrade and remove the old containers

# In the Terminal using Rancher-Compose

Following the steps from 
http://rancher.com/docs/rancher/v1.6/en/cattle/rancher-compose/, install rancher-compose from the Rancher Web UI, scrolling down to the page footer and selecting "Download CLI > Rancher Compose"

In the Rancher UI navbar, click on "API > Keys"

Click "Advanced Options"

Click "Add Environment API Key". Name the Key whatever you want. Once the Key is generated, copy+paste this into a secure place for future use. 

In the command line, run: 
```
export RANCHER_URL=http://server_ip:8080/
export RANCHER_ACCESS_KEY=<username_of_environment_api_key>
export RANCHER_SECRET_KEY=<password_of_environment_api_key>
```
using the Key you just generated

Navigate to the folder in which you have a docker-compose.yml file prepared. Make sure the name of the folder is the same as the stack you are updating. E.g. if the name of the stack is "Neuvector", the folder should be named "Neuvector". Otherwise rancher-compose will create a new stack with the name of the folder. 

Run: 
```
rancher-compose up -u --interval "30000"
```
If the image has changed but the tag has not, you can use the -p option to repull the image. 

Once the containers have finished updating, you can use
```
rancher-compose up -u -c
```
to confirm the upgrade and delete the old containers. 
