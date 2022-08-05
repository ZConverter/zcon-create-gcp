# README

# Create Instance Using Google cloud

---

### Auto Creating Instance Using Gcloud CLI

By following this guide, you can create Target VMs for ZConverter Cloud Management and Migration portal.

### ****Architecture Diagram****

![Untitled](./img/architecture.png)

---

# Before you begin

- Install or update to the latest version of the **Google Cloud CLI**
- You need to prepare a Google Account and enable **Compute Engine API**
- Prepare a SSH key for OS Login

For more information, see **[gcloud API Prerequisites](https://cloud.google.com/compute/docs/api/prereqs)**, **[goocle Compute Engine API](https://cloud.google.com/compute/?hl=en_US&_ga=2.127439885.-1431037956.1657087990&_gac=1.247445878.1658456250.Cj0KCQjw8uOWBhDXARIsAOxKJ2HwOrINrWuvsPqRY6B1P9IDbj1YBFuMmERpr_vx6kTjSa3D1nsni6saAupYEALw_wcB)**

### **Installation**

Before run gcp_main.exe, install **python**, **gcloud CLI**

See **[how to install gcloud CLI](https://cloud.google.com/sdk/docs/install)**

> Cloud SDK requires Python; supported versions are Python 3 (preferred, 3.5 to 3.8) and Python 2 (2.7.9 or later).
> 

### **Create service account key in Gcloud console**

To authorize the use of a service account key, you will need to save the service account credentials to your local system.

For more information about Service account, see **[Authorizing the gcloud CLI](https://cloud.google.com/sdk/docs/authorizing)**

1. Go to the **Sevice Accounts** page in the Google Cloud console
    
    ![Untitled](./img/IAM.png)
    
2. Choose an existing account or create a new accout by clicking **Create service account**.
    
    ![Untitled](./img/service_account.png)
    
3. Create new JSON-formatted key file:
    1. Click **KEYS**
    2. Click the **ADD KEY** drop down menu.
    3. Click **Create new key**
    4. Select a key format **JSON** and then click **Create** (It will be downloaded to your local system)
    5. Move your key file to a folder where you run the gcp_main.exe
    
    ![Untitled](./img/add_key.png)
    
    ![Untitled](./img/create_key.png)
    

### **Create SSH Key for OS Login**

If you want to connect VMs using third party tools or OpenSSH, you need to add a key to your VM. If you don’t have an SSH key, you can create one using `ssh-keygen`.

For more information, see **[Create an SSH key pair](https://cloud.google.com/compute/docs/connect/create-ssh-keys)**

**For Linux and macOS**

```
ssh-keygen -t rsa -f KEY_FILENAME -C USERNAME -b 2048
```

**For Windows 10 or later**

```
ssh-keygen -t rsa -f KEY_FILENAME -C USERNAME -b 2048
```

---

# Set up and Specify Instance spec

### 1. Clone this repository

Create a local copy of this repository:

```
git clone https://github.com/ZConverter/zcon-create-gcp.git
cd zcon-create-gcp
```

### 2. **Modify info.json and Create VMs**

For each instance you want to create, modify **info.json** about the values listed below. 

- You must specify **credentials_path, vm_name.**

<details>
<summary> Description about info.json </summary>
<div markdown="1">
1. credentials_path (string)
    
    The name of the credential key file created on the previous step. You must specify it.
    
    > You can create it by using either the Google Cloud Console or gcloud iam service-accounts keys create. The key file can be .json (preferred) or .p12 (legacy) format. In case of legacy .p12 files, a separate password might be required and is displayed in the Console when you create the key.
    > 
2. region (string)
    
    Region which specifies zone which you want to create the instance. The default region is “asia-northeast3”. If you want to display Compute Engine regions in a project, run **`gcloud compute region list`.**
    
3. OS and OS_version (string)
    
    The default OS and OS_version is windows-2016. Available operating system and version:
    
    | OS | OS version | Matching Image-family |
    | --- | --- | --- |
    | string | string |  |
    | centos | 7 / 8 / 9 | centos-7 / centos-stream-8 / centos-stream-9 |
    | rhel | 7/ 8 / 9 | rhel-7 / rhel-8 / rhel-9 |
    | ubuntu | 16.04 / 18.04 / 20.04 / 22.04 | ubuntu-pro-1604-lts / ubuntu-pro-1804-lts / ubuntu-pro-2004-lts / ubuntu-pro-2204-lts |
    | windows | 2012 / 2016 / 2019 / 2022 | windows-2012-r2 / windows-2016 / windows-2019 / windows-2022 |
4. vm_name (string)
    
    The name of the resource. To create VM, you must specify vm_name. The name must be unique to the project.
    
    > The resource name must be 1-63 characters long, and comply with RFC1035. Specifically, the name must be 1-63 characters long and match the regular expression [a-z]([-a-z0-9]*[a-z0-9])? which means the first character must be a lowercase letter, and all following characters must be a dash, lowercase letter, or digit, except the last character, which cannot be a dash.
    > 
5. machine_type (string)
    
    Specify the machine type used for the instances. If unspecified, the default type is n1-standard-1. To get a list of available machine types, run `gcloud compute machine-types list`.
    
6. username (string)
    
    A username of OS login to connect VMs. For Linux VMs, the username can’t be `root`. For Windows VMs, the username can’t be `Administrator`.
    
7. ssh_authorized_keys (string)
    
    A value of your public SSH key that use OS Login. The key must use the `public-openssh` format. For windows VM, not required.
    
    > If unspecified, VMs that use OS Login accept SSH keys that are associated with your Google account. You can add SSH keys to user account using Google Cloud console. For more information, see **[Add SSH keys to VMs](https://cloud.google.com/compute/docs/connect/add-ssh-keys#gcloud_1)**
    > 
8. volume (list)
    
    A list of the sizes of non-boot persistent disks to be created and attached to the instances. Disk size must be a multiple number of 1 GB. The default size is 100. If you want to create one or more disks, put sizes in the list. This persistent disk will be automatically deleted when the instance is deleted. 
    
    - If you create one or more additional disks for Windows VM, the first disk is created from same version image of VM’s OS, and other disks are blank disks.
    
    | OS image | Disk image |
    | --- | --- |
    | windows-2012-r2 | windows-server-2012-r2-dc-v20220712 |
    | windows-2016 | windows-server-2016-dc-v20220713 |
    | windows-2019 | windows-server-2019-dc-v20220712 |
    | windows-2022 | windows-server-2022-dc-v20220712 |
9. user_script (string)
    
    A startup script in a local file. The script will be executed by the instances once it starts running. You can use one of the following formats:
    
    **for Windows**
    
    ```
    <script>
    .....
    </script>
    ```
    
    **for Linux**
    
    ```
    #! /bin/bash
    ...
    ```
    
10. tags (`string`)
    
    Name and target tags of the firewall rules. The firewall rule applies only to instances with this tags. The tags must be unique to the project.
    
11. firewall_rule (list)
    
    A list of protocols and destination ports to allow inbound rules. The rules are created in the `default` network. To use firewall_rule, you must specifiy tags. For more information, see **[VPC firewall rules overview](https://cloud.google.com/vpc/docs/firewalls#direction_of_the_rule)**
    

**Example JSON**

```json
{
    "generate":{
        "cloud_platform": "gcp",
        "auth":{
            "credentials_path":"credentials.json",
            "region":"asia-northeast3"
          },
        "vm_info":{
            "OS" : "windows",
            "OS_version" : "2016",
            "vm_name" : "test",
            "machine_type" : "",
						"username":"user-test",
            "ssh_authorized_keys" : "ssh-rsa AAAA...",
            "volume" : [50,50,...],
            "user_script":"win_script.ps1",
            "tags":"zcm-vm",
            "firewall_rule":[
              "tcp:3389","tcp:139",...
            ]
        }
    }
  }
```
</div>
</details>
You can use one of the following steps depends on your case:

### The Steps for ZCM VMs

1. Check **./ZCM/info.json** and modify to create a ZCM VM
    1. Change **credentials_path** to the name of the credential key file created on the previous step. 
    2. Change **vm_name** to be unique to the project.
    3. If required, change other values 
2. Go to the folder where you run gcp_main.exe
3. Run the following commands:
    
    ```
    gcp_main.exe --info_json ./ZCM/info.json
    ```
    

### The Steps Target VMs

1. Check **./Target/[OS]/info.json** and modify to create a ZCM VM. For windows, **./Target/Windows/info.json**. For linux, **./Target/Linux/info.json**
    1. Change **credentials_path** to the name of the credential key file created on the previous step. 
    2. Change **vm_name** to be unique to the project.
    3. If required, change other values 
2. Go to the folder where you run gcp_main.exe
3. Run the following commands:
    - OS: Windows or Linux
    
    ```
    gcp_main.exe --info_json ./Target/[OS]/info.json
    ```
    

Takes a short period of time for the instance to start. After the instance is ready, you can see a log about VM instances. You can connect to a VM using following log. 

- If you are unable to connect Windows VMs, please wait a few more minutes and then try again.
- For Linux VM, you can log in using private key.

**Example Log** 

For Windows:

```
{
        "instance": {
                "Name": "test",
                "Public IP": "**.**.***.***",
                "Login": {
                        "Id": "user-test",
                        "Password": "********"
                }
        }
}
```

For Linux:

```
{
        "instance": {
                "Name": "test",
                "Public IP": "**.**.***.***",
                "Login": {
                        "Id": "user-test"
                }
        }
}
```

---

# Example Scenarios

Create an Windows 2016 VM instance

- OS: windows-2016
- zone: asia-northeast3-a
- Machine type: n1-standard-1
- Firewall: allow tcp traffic of 3389, 139, 445, 50000, 50001, 50002, 50003 ports
- Pesistent Disk: 50 GB disk of windows-server-2016-dc-v20220713, 50 GB blank disk
- OS Login ID: user-test1

```json
{
    "generate":{
        "cloud_platform": "gcp",
        "auth":{
            "credentials_path":"credentials.json",
            "region":"asia-northeast3"
          },
        "vm_info":{
            "OS" : "windows",
            "OS_version" : "2016",
            "vm_name" : "test1",
            "machine_type" : "",
						"username":"user-test1",
            "ssh_authorized_keys" : "",
            "volume" : [50,50],
            "user_script":"win_script.ps1",
            "tags":"zcm-vm",
            "firewall_rule":[
              "tcp:3389","tcp:139","tcp:445","tcp:50000","tcp:50001","tcp:50002","tcp:50003"
            ]
        }
    }
  }
```

Create a CentOS-8 VM instance

- OS: centos-8
- zone: asia-northeast3-a
- Machine type: e2-medium
- Pesistent Disk: 50 GB blank disk
- OS Login ID: user-test2

```json
{    
	"generate":{
        "cloud_platform": "gcp",
        "auth":{
            "credentials_path":"credentials.json",
            "region":"asia-northeast3"
          },
        "vm_info":{
            "OS" : "centos",
            "OS_version" : "8",
            "vm_name" : "test2",
            "machine_type" : "e2-medium",
						"username":"user-test2",
            "ssh_authorized_keys" : "ssh-rsa AAAA...",
            "volume": [50],
            "user_script":"lin_script.sh",
            "tags":"",
            "firewall_rule":[]
        }
    }
  }
```
