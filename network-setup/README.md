# Purpose

This container sets up dockerized dhcp and dns servers on a networked machine based on a configuration file.

The following manual tasks still needs to be done before running this container:
- Setup the routing
- Setup a static ip on the network interfaces of the machine hosting the dhcp/dns containers

From there, assuming your configuration is comprehensive, ip and name assignment will be handled from a centralized configuration file on the machine running the dhcp/dns servers.

This should work well in a network where the number of machines that need to have a fixed ip and known name is limited to at most ~20 machines (ex: home lab, small company, etc). 

In a larger setup, a dynamic system where machines requiring a fixed ip and a known name register themselves probably makes more sense.

# Usage

Change the content of the "conf" file for something that makes sense for your network.

The top-level entries under "networks" are the various networks the dhcp/dns server will server. The name of each entry represents the domain name that will be used on the network.

For example, if the entry is test, then a host with the name **hosta** will actually be have the name **hosta.test**.

The machines list is optional. If set, it will make sure that for each mac address specifie, the given machine will have to given ip and name (remember that the network domain name will be appended to that name to form the machine name).

Then, run:

```
docker-compose run setup
```

