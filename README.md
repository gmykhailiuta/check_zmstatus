# check_zmstatus
Nagios check to monitor zimbra services status.
Original taken from http://blog.schose.net/index.php/archives/135 and fixed.

This plugin check the state of the zimbra services, asking directly to the zmcointrol status, through zimbra user. 

Fixed issue with spaces in service names.

Original was taken from: http://blog.schose.net/index.php/archives/135, http://andreas.ndfnet.de/showfiles/blog/check_zmstatus.pl

We need to execute some additional commands, we need to put in the suoders file the next sentence /etc/sudoers :

    %nagios ALL=(zimbra) NOPASSWD:/opt/zimbra/bin/zmcontrol

And, in our nrpe.cfg file the next:

    [root@zimbra ~]# vim /etc/nagios/nrpe.cfg
    command[check_zmstatus]=/usr/lib64/nagios/plugins/check_zmstatus.pl -b $ARG1$
    [root@zimbra ~]# service nrpe restart


Now, in our Nagios server, this:


    root@srvnagios:~# vim /etc/nagios3/conf.d/services_nagios2.cfg
    define service {
      use generic-service
      hostgroup_name zimbra-servers
      service_description Zimbra Status
      check_command check_nrpe_zimbra
    }
   
And we need to add the next in our commands.cfg file:

    define command{
      command_name check_nrpe_zimbra
      command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c check_zmstatus -u
    }
