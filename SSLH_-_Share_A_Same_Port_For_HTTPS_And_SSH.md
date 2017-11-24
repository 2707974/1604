### SSLH - Share A Same Port For HTTPS And SSH

Some Internet service providers and corporate companies might have blocked most of the ports, and allowed only a few specific ports such as port 80 and 443 to tighten their security. In such cases, we have no choice, but use a same port for multiple programs, say the HTTPS Port 443, which is rarely blocked. Here is where SSLH, a SSL/SSH multiplexer, comes in help. It will listen for incoming connections on a port 443. To put this more simply, SSLH allows us  to run several programs/services on port 443 on a Linux system. So, you can use both SSL and SSH using a same port at the same time. If you ever been in a situation where most ports are blocked by the firewalls, you can use SSLH to access your remote server.

This brief tutorial describes how to share a same port for https, ssh using SSLH in Unix-like operating systems.
#### SSLH – Share A Same Port For HTTPS, SSH, And OpenVPN

Install SSLH

SSLH is packaged for most Linux distributions, so you can install it using the default package managers.

On Arch Linux and derivatives like Antergos, Manjaro Linux, install it using Pacman as shown below.

        sudo pacman -S sslh

On Debian, Ubuntu, and derivatives, run:

        sudo apt-get install sslh

On RHEL, CentOS, you need to add EPEL repository and then install SSLH as shown below.

        sudo yum install epel-release
        
        sudo yum install sslh

On Fedora:

        sudo dnf install sslh

#### Configure Apache or Nginx webservers

As you already know, Apache and Nginx webservers will listen on all network interfaces (i.e 0.0.0.0:443) by default. We need to change this setting to tell the webserver to listen on the localhost interface only (i.e 127.0.0.1:443 or localhost:443).

To do so, edit the webserver (nginx or apache) configuration file and find the following line:

        listen 443 ssl;

And, change it to:

        listen 127.0.0.1:443 ssl;

If you’re using Virutalhosts in Apache, make sure you have changed that it too.

        VirtualHost 127.0.0.1:443

Save and close the config files. Do not restart the services. We haven’t finished yet.

#### Configure SSLH

Once you have made the webservers to listen on local interface only, edit SSLH config file:

        sudo vi /etc/default/sslh

Find the following line:

        Run=no

And, change it to:

        Run=yes

Then, scroll a little bit down and modify the following line to allow SSLH to listen on port 443 on all available interfaces (Eg. 0.0.0.0:443).

        DAEMON_OPTS="--user sslh --listen 0.0.0.0:443 --ssh 127.0.0.1:22 --ssl 127.0.0.1:443 --pidfile /var/run/sslh/sslh.pid"

Where,

* user sslh : Requires to run under this specified username.
* listen 0.0.0.0:443 : SSLH is listening on port 443 on all available interfaces.
* sshs 127.0.0.1:22 : Route SSH traffic to port 22 on the localhost.
* ssl 127.0.0.1:443 : Route HTTPS/SSL traffic to port 443 on the localhost.

Save and close the file.

Finally, enable and start sslh service to update the changes.

        sudo systemctl enable sslh

        sudo systemctl start sslh

#### Testing

Check if the SSLH daemon is listening to 443.

        $ ps -ef | grep sslh 
        sslh 2746 1 0 15:51 ? 00:00:00 /usr/sbin/sslh --foreground --user sslh --listen 0.0.0.0 443 --ssh 127.0.0.1 22 --ssl 127.0.0.1 443 --pidfile /var/run/sslh/sslh.pid
        sslh 2747 2746 0 15:51 ? 00:00:00 /usr/sbin/sslh --foreground --user sslh --listen 0.0.0.0 443 --ssh 127.0.0.1 22 --ssl 127.0.0.1 443 --pidfile /var/run/sslh/sslh.pid
        sk 2754 1432 0 15:51 pts/0 00:00:00 grep --color=auto sslh

Now, you can access your remote server via SSH using port 443:

    $ ssh -p 443 sk@192.168.43.2
    sk@192.168.43.2's password: 
    Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-89-generic x86_64)
    
    * Documentation: https://help.ubuntu.com
    * Management: https://landscape.canonical.com
    * Support: https://ubuntu.com/advantage
    
    Last login: Mon Aug 14 15:52:20 2017 from 192.168.43.192
    sk@ubuntuserver:~$

See? I can now be able to access the remote server via SSH even if the default SSH port 22 is blocked. As you see in the above example, I have used the https port 443 for SSH connection. Also, we can use the same port 443 for openVPN connections too. For more details, check the project’s website URL given below.
