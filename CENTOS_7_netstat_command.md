`netstat` is a command line utility to view the statistics of network connection to/from the machines. 
With `netstat`, you can see network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

Are you wondering? netstat command not found after the installation CentOS 7 / RHEL 7.

    -bash: netstat: command not found

This small guide will help you to install the necessary packages for getting `netstat` command.

Let us see which package provides us `netstat` command.

    yum whatprovides netstat
    
Output:

    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: centos.chi.host-engine.com
     * epel: mirror.steadfast.net
     * extras: repo.us.bigstepcloud.com
     * updates: centos.chi.host-engine.com
    net-tools-2.0-0.17.20131004git.el7.x86_64 : Basic networking tools
    Repo        : base
    Matched from:
    Filename    : /bin/netstat
    
From the above command, you can see that `net-tools` package provides you `netstat` command. So, install the net-tools package using the yum command.

    yum -y install net-tools
   
Output:

    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * base: centos.chi.host-engine.com
     * epel: mirror.steadfast.net
     * extras: repo.us.bigstepcloud.com
     * updates: centos.chi.host-engine.com
    Resolving Dependencies
    --> Running transaction check
    ---> Package net-tools.x86_64 0:2.0-0.17.20131004git.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ====================================================================================================================================================================================================
     Package                                      Arch                                      Version                                                       Repository                               Size
    ====================================================================================================================================================================================================
    Installing:
     net-tools                                    x86_64                                    2.0-0.17.20131004git.el7                                      base                                    304 k

    Transaction Summary
    ====================================================================================================================================================================================================
    Install  1 Package

    Total download size: 304 k
    Installed size: 917 k
    Downloading packages:
    net-tools-2.0-0.17.20131004git.el7.x86_64.rpm                                                                                                                                | 304 kB  00:00:00     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : net-tools-2.0-0.17.20131004git.el7.x86_64                                                                                                                                        1/1 
      Verifying  : net-tools-2.0-0.17.20131004git.el7.x86_64                                                                                                                                        1/1 

    Installed:
      net-tools.x86_64 0:2.0-0.17.20131004git.el7                                                                                                                                                       

    Complete!

Once the installation is complete, run `netstat` to see whether it is available or not.

    netstat
    

    That’s All. You now have netstat command available on your CentOS 7 machine.
