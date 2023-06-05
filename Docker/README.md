# From inside of a Docker container, how do I connect to the localhost of the machine

docker run --network="bridge" (default)
Docker creates a bridge named docker0 by default. Both the docker host and the docker containers have an IP address on that bridge.

on the Docker host, type sudo ip addr show docker0 you will have an output looking like:

[vagrant@docker:~] $ sudo ip addr show docker0
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
link/ether 56:84:7a:fe:97:99 brd ff:ff:ff:ff:ff:ff
inet 172.17.42.1/16 scope global docker0
valid_lft forever preferred_lft forever
inet6 fe80::5484:7aff:fefe:9799/64 scope link
valid_lft forever preferred_lft forever
So here my docker host has the IP address 172.17.42.1 on the docker0 network interface.
host mode
To access MySQL running on the docker host from containers in host mode, you can keep bind-address = 127.0.0.1 in your MySQL configuration and connect to 127.0.0.1 from your containers:

[vagrant@docker:~] $ docker run --rm -it --network=host mysql mysql -h 127.0.0.1 -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 36
Server version: 5.5.41-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its affiliates. Other names may be trademarks of their respective owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
note: Do use mysql -h 127.0.0.1 and not mysql -h localhost; otherwise the MySQL client would try to connect using a unix socket.