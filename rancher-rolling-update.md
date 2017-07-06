# Rolling Update in Rancher 1.6
# In the Web UI
(Source: http://rancher.com/docs/rancher/v1.6/en/cattle/upgrading/#in-service-upgrade)

Select the targetted stack which contains the service to upgrade

Select the service to upgrade

In the upper right-hand corner, there is an "upgrade" button shaped like ⬆️. Select this button.

For "Batch Size", enter "1". This will cause one container to be upgraded at a time. For "Batch Interval", set 30 seconds. This will ensure there is enough time for data transfer between old and new containers. Leave "Start Behavior" unchecked. 

Enter the desired URL to pull the image from

Scroll down, and if desired, set the "Health Check" to something appropriate

Click "Upgrade"

Once the containers have finished updating, click the button in the top right-hand corner to confirm the upgrade and remove the old containers

# In the Terminal using Rancher-Compose

Install rancher-compose from the Rancher Web UI, scrolling down to the page footer and selecting "Download CLI > Rancher Compose", and following the steps from: 

http://rancher.com/docs/rancher/v1.6/en/cattle/rancher-compose/

