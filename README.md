Linux monitoring template
===============================

Linux monitoring
-------------------

Monitoring the resources of a Linux server.

* Use the Linux standard command which collect the server performance information.
* Create a graph of the CPU, memory, disk, network resources.
* Using Zabbix Linux standard template, monitoring the Linux event (optional).
* RedHat5,6, Ubuntu12,14 supports. It is required agent compile in each platform.

**[Notice]**

* Linux monitoring template is bundled with the base module. You do not need to set up the template separately.

Configuration
------------

File configuration of the template is as follows.

|            Directory            |         Filename         |            Application             |
|---------------------------------|--------------------------|------------------------------------|
| lib/agent/Linux/conf/           | ini file                 | Agent command list file            |
| lib/agent/Linux/script/         | Script                   | Agent Collecion script             |
| lib/Getperf/Command/Site/Linux/ | Perl                     | Data Aggrigation script            |
| lib/graph/Linux/                | json file                | Cacti graph template rule          |
| lib/zabbix/Linux.json           | json file                | Zabbix template rule               |
| lib/cacti/template/0.8.8g/      | xml file                 | Cacti template export file         |
| script/                         | create_graph_template.sh | Graph template registration script |

Metric
-----------

Monitoring item definitions such as performance statistics graph is as follows.

|            Key             |                                    Description                                    |
|----------------------------|-----------------------------------------------------------------------------------|
| **Performance Statistics** | **Server resource graph**                                                         |
| vmstat                     | **CPU / Memory**<br> CPU Util% / CPU RunQue　/ Free Memory / Swap rate            |
| loadavg                    | **Load Average** <br> CPU Load Average                                            |
| memfree                    | **Memory** <br> Memory Usage / Memory Usage Detail                                |
| iostat                     | **Disk I/O** <br> Disk busy% / Disk IOPS / Disk transfer rate / Disk I/O response |
| netDev                     | **Networ I/O**<br> Netowrk packets / Network transfer rate / Netowrk errors       |
| diskutil                   | **Disk Usage**<br> Disk usage                                                     |
| **Event**                  | **Zabbix monitoring**                                                             |
| Linux                      | Monitoring by Linux template of Zabbix standard template                          |

Install
=======

Build the template
--------------------

Clone the project from Git Hub.

```
git clone https://github.com/getperf/t_Linux
```

Go to the project directory, initialize the site with the --template options.

```
cd t_Linux
initsite --template .
```

Run the Cacti graph templates creation script.

```
./script/create_graph_template__linux.sh
```

Export the Cacti graph templates.

```
cacti-cli --export Linux
```

Archive the aggregate script, graph registration rules, export file and Cacti graph templates.

```
mkdir -p $GETPERF_HOME/var/template/archive/
sumup --export=Linux --archive=$GETPERF_HOME/var/template/archive/config-Linux.tar.gz
```

Import Template
------------------------

Unzip the archive file that you created in the previous to the monitoring site.

```
cd {Monitoring site home}
tar xvf $GETPERF_HOME/var/template/archive/config-Linux.tar.gz
```

Import the Cacti graph templates.

```
cacti-cli --import Linux
```

To reflect the imported aggregate script, Restart the counting daemon.

```
sumup restart
```

Agent Setup
========================

Deploy the scripts
--------------------

Deploy the Linux data collection library to the Linux server to be monitored.
Copy the file the following directory of the monitoring site to the monitored agent home directory.

```
ls lib/agent/Linux/
conf  script
scp lib/agent/Linux/* {OS User}@{Linux server}:~/ptune/
```

Start the agent
------------------

To reflect the setting, and then restart the agent.

```
~/ptune/bin/getperfctl stop
~/ptune/bin/getperfctl start
```

Cacti graph registration
========================

After the agent setup, and data aggregation is performed, site node definition file under the node of the home directory will be output.
Specify the output node defined directory and run the cacti-cli.

```
cd {site home}
cacti-cli node/Linux/{Linux server}/
```

Zabbix registration
===================

Regist IP to the .hosts file
----------------------------

Without DNS, if your monitoring server host name can not see the IP address, you can set the IP address to the .hosts file.

```
cd {site home}
vi .hosts
```

Please register the IP address in the format of "IP hostname".

Monitoring settings of Zabbix
------------------------------

Run zabbix-cli command, after the confirmation of the settings in the --info option, register with the --add option.

```
# Check the registration
zabbix-cli --info node/Linux/{Linux server}/
# Regist the registration
zabbix-cli --add node/Linux/{Linux server}/
```

AUTHOR
-----------

Minoru Furusawa <minoru.furusawa@toshiba.co.jp>

COPYRIGHT
-----------

Copyright 2014-2016, Minoru Furusawa, Toshiba corporation.

LICENSE
-----------

This program is released under [GNU General Public License, version 2](http://www.gnu.org/licenses/gpl-2.0.html).
