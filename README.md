MaxiNet - Distributed Software Defined Network Emulation
========================================================
This fork enables connecting containers on the same machine without using a switch.

Installation
=
## Automated installation

In case you want to use the automated installer, just issue the following command:
```bash
./installer.sh
```

## On Ubuntu
If you are running Ubuntu, you now have to setup your user to use sudo without password. This can simply be done by adding the following line to your /etc/sudoers file.<br>
Replace *yourusername* with your user name.
```bash
yourusername ALL=(ALL) NOPASSWD: ALL
```

## Manual installation

1: Install dependencies
```bash
sudo apt-get install git autoconf screen cmake build-essential sysstat python-matplotlib uuid-runtime python-pip
```

2: Install Mininet 2.2.1rc1 and OpenVSwitch
```bash
git clone git://github.com/mininet/mininet
cd mininet
git checkout -b 2.2.1rc1 2.2.1rc1
cd .. && sudo mininet/util/install.sh -a
```

3: Install Metis
```bash
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/metis/metis-5.1.0.tar.gz
tar -xzf metis-5.1.0.tar.gz
rm metis-5.1.0.tar.gz
cd metis-5.1.0
 make config
make
sudo make install
cd ..
```

4: Install Pyro4
```bash
sudo pip install Pyro4
```

5: Download and install MaxiNet
```bash
cd ~ && git clone https://github.com/sammykop/MaxiNet.git
cd MaxiNet
git checkout v1.2
sudo make install
```

6: Set up cluster and configure MaxiNet

Repeat steps 1-5 for every worker or frontend.

On the frontend machine:<br>
Copy MaxiNet-cfg-sample file to ~/.MaxiNet.cfg<br>
```bash
sudo cp ~/MaxiNet/share/MaxiNet-cfg-sample /etc/MaxiNet.cfg
```
Edit the configuration file.<br>
Look at this [wiki page](https://github.com/MaxiNet/MaxiNet/wiki/Configuration-File) for more information about the configuration files.

Note that under Ubuntu, you need to set:
```bash
[all]
...
sshuser = yourusername
usesudo = True
```

Please note that every worker connecting to the MaxiNet Server will need an respective entry in the configuration file, named by its hostname and containing its ip. The IP-section can look like this:<br>
```bash
[FrontendServer]
ip = 192.168.123.1
threadpool = 256 
(each Worker requires 2 threads on the FrontendServer)

[worker1-machine]
ip = 192.168.123.1
share = 1

[worker2-machine]
ip = 192.168.123.2
share = 1
```
Although MaxiNet tries to guess the IP of the worker if not found in the
configuration file this is nothing one should depend on.
Copy the ./MaxiNet.cfg file to all worker machines.

7: Startup FrontendServer and workers:<br>

On the frontend machine call:
```bash
screen -d -m -S MaxiNetFrontend MaxiNetFrontendServer
```
On every worker machine call:

```bash
screen -d -m -S MaxiNetWorker sudo MaxiNetWorker
```
You should see that the workers are connecting to the frontend.<br>
Start an OpenFlow controller. This controller is needed for the switches connecting both workers.
```bash
cd ~/pox/ && python2 pox.py forwarding.l2_learning
```

8: Test your cluster:
Make the following call on any Worker-machine connected to the frontend:
```bash
maxinet@worker2:~$ MaxiNetStatus
MaxiNet Frontend server running at 192.168.0.1
Number of connected workers: 2
--------------------------------
worker1		free
worker2		free
```

References and more information:<br>
https://maxinet.github.io/<br>
https://github.com/MaxiNet/MaxiNet<br>
https://github.com/MaxiNet/MaxiNet/wiki/FAQ<br>
https://github.com/MaxiNet/MaxiNet/wiki/Configuration-File<br>
https://rawgit.com/MaxiNet/MaxiNet/v1.0/doc/maxinet.html<br>



