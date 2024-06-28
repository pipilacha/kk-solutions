# Task
Some users of the monitoring app have reported issues with xFusionCorp Industries mail server. 

They have a mail server in Stork DC where they are using postfix mail transfer agent. 

Postfix service seems to fail. Try to identify the root cause and fix it.

# Solution

1. In file /etc/postfix/main.cf
   * There are two definitions for ```inet_interfaces```, set it to listen on all interfaces
       * ```inet_interfaces = all```
   * Set to use IPv4 only ```inet_protocols = ipv4```
