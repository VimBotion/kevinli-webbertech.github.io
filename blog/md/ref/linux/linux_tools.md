# Linux Tools Ref

In my example, I mostly use ubuntu 22 LTS when I wrote this article.
What have been covered in this articles are:

* Samba server
* SSH server
* HTTP server - Apache2
* SendMail
* GTK

## Samba Server

### What is Samba?

Samba is a free, open-source software suite that provides file and print services to SMB/CIFS clients, allowing interoperability between Unix/Linux and Windows machines. Here are some key aspects of Samba:

File and Print Sharing: Samba allows Unix/Linux systems to share files and printers with Windows clients and vice versa. It uses the SMB (Server Message Block) protocol, which is also known as CIFS (Common Internet File System).

Interoperability: Samba makes it possible for Unix/Linux systems to appear as Windows servers on a network, allowing seamless file sharing between different operating systems.

Domain Controller: Samba can function as a Primary Domain Controller (PDC) or a Backup Domain Controller (BDC) in a Windows NT domain. It can also join Active Directory as a member.

Authentication and Authorization: Samba can handle user authentication and authorization, integrating with various backend systems such as LDAP, Active Directory, and local Unix/Linux password files.

Network Browsing: Samba allows Unix/Linux machines to participate in Windows network browsing, making them visible in the Network Neighborhood or My Network Places on Windows machines.

Key Components of Samba
smbd: The SMB/CIFS server daemon that provides file and print services to SMB/CIFS clients.

nmbd: The NetBIOS name server daemon that handles NetBIOS name resolution and browsing.

winbindd: This service allows for the integration of Unix/Linux systems with Windows NT-based systems, enabling domain authentication.

Common Use Cases
Home Networks: Sharing files and printers between different operating systems within a home network.
Office Networks: Centralized file and print services in mixed OS environments, often replacing or supplementing Windows servers.
Domain Services: Acting as a domain controller in small to medium-sized networks.

### Installation

To start a Samba server (also known as a Samba "driver") on a Linux system, you need to install the Samba package, configure it, and then start the Samba services. Here's a step-by-step guide:

```bash
sudo apt update
sudo apt install samba
```

### Configuration

Configure Samba:

Edit the Samba configuration file `/etc/samba/smb.conf` to define your shared directories and settings. For example:

```bash
[shared]
path = /srv/samba/shared
read only = no
browsable = yes
```

### Create the Shared Directory

```bash
sudo mkdir -p /srv/samba/shared
sudo chown nobody:nogroup /srv/samba/shared
sudo chmod 0775 /srv/samba/shared
```

### Add Samba Users

Add a user to Samba (the user must already exist on the system):

`sudo smbpasswd -a username`

### Start the Samba Services

```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
sudo systemctl restart nmbd
sudo systemctl enable nmbd
```

### Verify the Samba Configuration

Check the Samba configuration for any syntax errors, `testparm`.

### Access the Share

From a Windows machine or another Samba client, you can access the share by navigating to
`\\hostname\shared` where hostname is the name of your Samba server.

for Example, from Mac, you can do this,

`smb://xiaofengli@192.168.0.105`

### Check logs

If you encounter any issues, checking the Samba logs can be helpful. They are usually located in
`/var/log/samba/`

## SSH Server

### What is SSH server

An SSH server allows remote access to a machine securely over an encrypted network connection. SSH (Secure Shell) is commonly used to manage servers, perform remote logins, and execute commands.

On most Linux distributions, the SSH server software is called OpenSSH. You can install it using the package manager for your distribution. Here’s how to install OpenSSH on some common Linux distributions.

### Installation

Debian/Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server
```

CentOS/RHEL:

```bash
sudo yum install openssh-server
```

Fedora:

`sudo dnf install openssh-server`

### Starting the SSH Service

Debian/Ubuntu:

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

CentOS/RHEL/Fedora:

```bash
sudo systemctl start sshd
sudo systemctl enable sshd
```

### Configuration

The main configuration file for OpenSSH is /etc/ssh/sshd_config. You can modify this file to change settings like the default port, authentication methods, and more.

* Change the SSH Port:

By default, SSH listens on port 22. You can change this to a different port for added security.

`Port 2222`

* Disable Root Login

`PermitRootLogin no`

* Allow Only Specific Users

`AllowUsers username1 username2`

### Connecting to the SSH Server

* Normal login to default port 22

`ssh username@server_ip_or_hostname`

* If you change your port,

`ssh -p username@server_ip_or_hostname`

* When your server is in private IP, then you will need to ssh to have a pem file,

`ssh -i pem_file username@server_ip_or_hostname`

### Security

* Use Public Key Authentication: Public key authentication is more secure than password-based authentication. You can set this up by generating an SSH key pair and adding the public key to the `~/.ssh/authorized_keys` file on the server

`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

* Copy the public key to the server

`ssh-copy-id username@server_ip_or_hostname`

* Disable Password Authentication

`PasswordAuthentication no`

* Only allowed IP can access and use firewall

Use a Firewall: Ensure that only trusted IP addresses can access your SSH server. You can configure this using tools like ufw (Uncomplicated Firewall) or firewalld.

## HTTP server - Apache2

Apache2 is the http server.

### Installation

`sudo apt install apache2`

Check where it is installed,

```bash
xiaofengli@xiaofenglx:~/code/scanhub$ which apache2
/usr/sbin/apache2

xiaofengli@xiaofenglx:/etc/init.d$ ls -al apache2
-rwxr-xr-x 1 root root 8181 Dec  4  2023 apache2
```

Check the above startup script in `/etc/init.d` dir,

![apache2]((https://kevinli-webbertech.github.io/blog/images/linux/apache2.png))

## Start/Stop/Status

`sudo service apache2 start`
`sudo service apache2 stop`
`sudo service apache2 restart`
`sudo service apache2 reload`
`sudo service apache2 status`

## SendMail

### Installation

`sudo apt update && sudo apt upgrade -y `

`sudo apt install -y sendmail sendmail-cf mailutils`

### Configure Sendmail

The main configuration file for Sendmail is /etc/mail/sendmail.cf. However, it is recommended to make changes to the .mc file (e.g., /etc/mail/sendmail.mc) and then generate the .cf file. This makes the configuration process easier and less error-prone.

To configure Sendmail, open the /etc/mail/sendmail.mc file using your preferred text editor:

`sudo nano /etc/mail/sendmail.mc`

Ensure the following lines are present and uncommented in the file:

```bash
define(`SMART_HOST', `your.smtp.server')dnl
define(`confAUTH_MECHANISMS', `EXTERNAL GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')dnl
FEATURE(`authinfo',`hash -o /etc/mail/authinfo.db')dnl
```

### Set Up Authentication (Optional)

If your SMTP server requires authentication, create the /etc/mail/authinfo file with the following contents:

AuthInfo:your.smtp.server "U:your_username" "P:your_password" "M:PLAIN"

Replace your.smtp.server, your_username, and your_password with the appropriate values for your SMTP server.

To create the authentication database, run:

`sudo makemap hash /etc/mail/authinfo < /etc/mail/authinfo`

## GTK

GTK (formerly GIMP ToolKit and GTK+) is a free software cross-platform widget toolkit for creating graphical user interfaces (GUIs). It is licensed under the terms of the GNU Lesser General Public License, allowing both free and proprietary software to use it. It is one of the most popular toolkits for the Wayland and X11 windowing systems.

The GTK team releases new versions on a regular basis. GTK 4 and GTK 3 are maintained, while GTK 2 is end-of-life. GTK1 is independently maintained by the CinePaint project. GTK 4 dropped the + from the name.

* GTK Drawing Kit (GDK): GDK acts as a wrapper around the low-level functions provided by the underlying windowing and graphics systems.

## Slack

Open slack from chrome browser,

`chrome --app=https://app.slack.com/client`

## sysbench

Test your CPU benchmark,

`sudo apt-get install sysbench`

* To do CPU benchmarking you can use:

`sysbench cpu run`

* This will run a single-threaded CPU benchmark. To use all cores, use:

`sysbench --threads="$(nproc)" cpu run`

## 7zip benchmark

To run a single-thread benchmark: 7z b -mmt1

To run a multi-thread benchmark: 7z b

## CPU-Z, CPU-X

* CPU-Z for windows
* CPU-X for Linux

You can also use CPU-X from the command line, as there are two options available for using CPU-X in the terminal, i.e., `NCurses` and `Coredump`.

Firstly, to access the NCurses interface use the following command.

`cpu-x -N`

![cpu-x](cpu-x.png)

Secondly, to get a summary of data, enter the following command.

`cpu-x -D`

```shell
xiaofengli@xiaofenglx:~$ cpu-x -D
Your CPU socket is not present in the database ==> Intel(R) Core(TM) i5-4690K CPU @ 3.50GHz, codename: Haswell (Core i5)
CPU-X:core.c:1637: failed to retrieve CPU voltage (fallback mode)
  >>>>>>>>>> CPU <<<<<<<<<<

	***** Processor *****
          Vendor: Intel
       Code Name: Haswell (Core i5)
         Package: 
      Technology: 22 nm
         Voltage: 
   Specification: Intel(R) Core(TM) i5-4690K CPU @ 3.50GHz
          Family: 0x6
     Ext. Family: 0x6
           Model: 0xC
      Ext. Model: 0x3C
           Temp.: 49.00°C
        Stepping: 3
    Instructions: MMX, SSE(1, 2, 3, 3S, 4.1, 4.2), AVX(1, 2), FMA(3), AES, CLMUL, RdRand, VT-x, x86-64

	***** Clocks *****
      Core Speed: 3478 MHz
      Multiplier: 
       Bus Speed: 
           Usage:  16.82 %

	***** Cache *****
         L1 Data: 4 x 32 kB, 8-way
        L1 Inst.: 4 x 32 kB, 8-way
         Level 2: 4 x 256 kB, 8-way
         Level 3: 6 MB, 12-way

	***** * *****
       Socket(s): 1
         Core(s): 4
       Thread(s): 4


  >>>>>>>>>> Caches <<<<<<<<<<

	***** L1 Cache *****
            Size: 4 x 32 kB, 8-way associative, 64-bytes line size
           Speed: 111219.40 MB/s

	***** L2 Cache *****
            Size: 4 x 256 kB, 8-way associative, 64-bytes line size
           Speed: 63140.90 MB/s

	***** L3 Cache *****
            Size: 6 MB, 12-way associative, 64-bytes line size
           Speed: 33885.40 MB/s


  >>>>>>>>>> Motherboard <<<<<<<<<<

	***** Motherboard *****
    Manufacturer: Gigabyte Technology Co., Ltd.
           Model: Z97X-UD5H
        Revision: x.x

	***** BIOS *****
           Brand: American Megatrends Inc.
         Version: F8
            Date: 06/17/2014
        ROM Size: 

	***** Chipset *****
          Vendor: Intel Corporation
           Model: Z97 Chipset LPC Controller


  >>>>>>>>>> Memory <<<<<<<<<<


  >>>>>>>>>> System <<<<<<<<<<

	***** Operating System *****
          Kernel: Linux 6.8.0-45-generic
    Distribution: Ubuntu 22.04.5 LTS
        Hostname: xiaofenglx
          Uptime: 0 days, 5 hours, 8 minutes, 28 seconds
        Compiler: cc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0

	***** Memory *****
            Used: 9.30 GiB / 31.19 GiB
         Buffers: 0.45 GiB / 31.19 GiB
          Cached: 7.20 GiB / 31.19 GiB
            Free: 14.26 GiB / 31.19 GiB
            Swap: 0.00 GiB / 2.00 GiB


  >>>>>>>>>> Graphics <<<<<<<<<<

	***** Card 0 *****
          Vendor: Intel
          Driver: i915
     UMD Version: Mesa 23.2.1-1ubuntu3.1~22.04.2
           Model: Xeon E3-1200 v3/4th Gen Core Processor Integrated Graphics Controller
        DeviceID: 0x0412:0x06
       Interface: 
     Temperature: 
           Usage: 
    Core Voltage: 
       Power Avg: 
       GPU clock: 600 MHz
    Memory clock: 
     Memory Used: 
```

### ref

- https://www.gtk.org/
- DevConf.cz
