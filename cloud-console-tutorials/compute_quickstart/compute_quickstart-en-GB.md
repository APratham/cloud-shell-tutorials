# Compute Engine Quickstart

## Build a to-do app with MongoDB

<walkthrough-tutorial-duration duration="15"></walkthrough-tutorial-duration>

In this quickstart, you'll use Compute Engine to create a two-tier application.
The frontend VM runs a Node.js to-do web app, and the back-end VM runs MongoDB.

This tutorial will walk you through:

*   Creating and configuring two VMs
*   Setting up firewall rules
*   Using SSH to install packages on your VMs

## Project setup

Google Cloud Platform organises resources into projects. This allows you to
collect all the related resources for a single application in one place.

<walkthrough-project-billing-setup permissions="compute.instances.create"></walkthrough-project-billing-setup>

## Navigate to Compute Engine

Open the [menu][spotlight-console-menu] on the left-hand side of the console.

Then, select the **Compute Engine** section.

<walkthrough-menu-navigation sectionid="COMPUTE_SECTION"></walkthrough-menu-navigation>

## Create instances

You will create 2 instances to be back-end and front-end servers for the
application.

### Create the back-end instance

First, create the back-end instance that runs MongoDB. This server stores the
to-do items.

Click the [Create instance][spotlight-create-instance] button.

*   Select a [name][spotlight-instance-name] and [zone][spotlight-instance-zone]
    for this instance.

*   Select [f1-micro][spotlight-machine-type]. This will incur fewer charges.

    *   [Learn more about pricing][pricing]

*   Select [Ubuntu 14.04 LTS][spotlight-boot-disk] as your boot disk image for
    this tutorial.

*   In the [Firewall selector][spotlight-firewall], select **Allow HTTP
    traffic**. This opens port 80 (HTTP) to access the app.

*   Click the [Create][spotlight-submit-create] button to create the instance.

Note: Once the instance is created, your billing account will start being
charged according to the GCE pricing. You will remove the instance later to
avoid extra charges.

### Create the front-end instance

While the back-end VM is spinning up, create the front-end instance that runs
the Node.js to-do application. Use the same configuration as the back-end
instance.

## Setup

You'll install a MongoDB database on the back-end instance to save your data.

The [SSH buttons][spotlight-ssh-buttons] in the table will open up an SSH
session to your instance in a separate window.

For this tutorial, you will connect using Cloud Shell. Cloud Shell is a built-in
command line tool for the console.

### Open Cloud Shell

Open Cloud Shell by clicking on the
<walkthrough-cloud-shell-icon></walkthrough-cloud-shell-icon>
[icon][spotlight-open-devshell] in the navigation bar at the top of the console.

### Wait for the instance creation to finish

The instances need to finish creating before the tutorial can proceed. The
activity can be tracked by clicking the
[notification menu][spotlight-notification-menu] from the navigation bar at the
top.

## Connect to your back-end instance

### Connect to the instance

Enter the following command to SSH into the VM. If this is your first time using
SSH from Cloud Shell, you will need to create a private key. Enter the zone and
name of the instance that you have created.

```bash
gcloud compute --project "{{project-id}}" ssh --zone <backend-zone> <backend-name>
```

### Install the back-end database

Now that you have an SSH connection to the back-end instance, use the following
commands to install the back-end database:

Update packages and install MongoDB. When asked if you want to continue, type
`Y`:

```bash
sudo apt-get update
```

```bash
sudo apt-get install mongodb
```

### Run the database

The MongoDB service started when you installed it. You must stop it so that you
can change how it runs.

```bash
sudo service mongodb stop
```

Create a directory for MongoDB and then run the MongoDB service in the
background on port 80.

```bash
sudo mkdir $HOME/db
```

```bash
sudo mongod --dbpath $HOME/db --port 80 --fork --logpath /var/tmp/mongodb
```

After this, exit the SSH session using the `exit` command:

```bash
exit
```

## Connect to your front-end instance

### Install and run the web app on your front-end VM

The MongoDB back-end server is running, so it is time to install the front-end
web application

### Connect to the instance

Enter the following command to SSH into the VM. Enter the zone and name of the
instance that you have created.

```bash
gcloud compute --project "{{project-id}}" ssh --zone <frontend-zone> <frontend-name>
```

### Install the dependencies

Now that you're connected to your front-end instance, update packages and
install git, Node.js and npm. When asked if you want to continue, type `Y`:

```bash
sudo apt-get update
```

```bash
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
```

```bash
sudo apt-get install git nodejs
```

### Install and run the front-end web app

Clone the sample application and install application dependencies.

```bash
git clone https://github.com/GoogleCloudPlatform/todomvc-mongodb.git
```

```bash
cd todomvc-mongodb; npm install
```

```bash
sed -i -e 's/8080/80/g' server.js
```

Start the to-do web application with the following command, entering the
[internal IP addresses][spotlight-internal-ip] for the instances that you have
created.

```bash
sudo nohup nodejs server.js --be_ip <backend-internal-ip> --fe_ip <frontend-internal-ip> &
```

After this, exit the SSH session using the `exit` command:

```bash
exit
```

### Visit your application

Visit your webserver at the IP address listed in the
[External IP][spotlight-external-ip] column next to your front-end instance.

## Cleanup

To remove your instances, select the [checkbox][spotlight-instance-checkbox]
next to your instance names and click the
[Delete button][spotlight-delete-button].

## Conclusion

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>

You've finished!

Here's what you can do next:

*   Find Google Cloud Platform
    [samples on GitHub](http://googlecloudplatform.github.io/).

*   Learn how to set up
    [Load Balancing](https://cloud.google.com/compute/docs/load-balancing/).

*   Learn how to
    [transfer files to your Virtual Machine](https://cloud.google.com/compute/docs/instances/transfer-files/).

*   Learn how to
    [run containers](https://cloud.google.com/compute/docs/containers).

[pricing]: https://cloud.google.com/compute/#compute-engine-pricing
[spotlight-boot-disk]: walkthrough://spotlight-pointer?cssSelector=vm-set-boot-disk
[spotlight-console-menu]: walkthrough://spotlight-pointer?spotlightId=console-nav-menu
[spotlight-control-panel]: walkthrough://spotlight-pointer?cssSelector=#p6n-action-bar-container-main
[spotlight-create-instance]: walkthrough://spotlight-pointer?spotlightId=gce-zero-new-vm,gce-vm-list-new
[spotlight-delete-button]: walkthrough://spotlight-pointer?cssSelector=.p6n-icon-delete
[spotlight-external-ip]: walkthrough://spotlight-pointer?cssSelector=.p6n-external-link
[spotlight-firewall]: walkthrough://spotlight-pointer?spotlightId=gce-vm-add-firewall
[spotlight-instance-checkbox]: walkthrough://spotlight-pointer?cssSelector=.p6n-checkbox-form-label
[spotlight-instance-name]: walkthrough://spotlight-pointer?spotlightId=gce-vm-add-name
[spotlight-instance-zone]: walkthrough://spotlight-pointer?spotlightId=gce-vm-add-zone-select
[spotlight-internal-ip]: walkthrough://spotlight-pointer?cssSelector=gce-internal-ip
[spotlight-machine-type]: walkthrough://spotlight-pointer?spotlightId=gce-add-machine-type-select
[spotlight-notification-menu]: walkthrough://spotlight-pointer?cssSelector=.p6n-notification-dropdown,.cfc-icon-notifications
[spotlight-open-devshell]: walkthrough://spotlight-pointer?spotlightId=devshell-activate-button
[spotlight-ssh-buttons]: walkthrough://spotlight-pointer?cssSelector=gce-connect-to-instance
[spotlight-submit-create]: walkthrough://spotlight-pointer?spotlightId=gce-submit
[spotlight-vm-list]: walkthrough://spotlight-pointer?cssSelector=vm2-instance-list%20.p6n-checkboxed-table
