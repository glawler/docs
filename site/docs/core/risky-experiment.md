# Risky Experiment Support

We define as a risky experiment each experiment that either uses some type of malware such as DoS tool, a worm, an exploit, etc, even when the malware code is written by the experimenter and/or requires connectivity to the outside directly from experimental nodes. We recognize all these experiment types are interesting and important to facilitate new research in security. At the same time there is potential risk to the testbed and the Internet that must be contained. This risk includes:

- Malware interfering with other experiments on DETER
- Malware escaping to the outside world
- Malware overwhelming critical infrastructure in DETER, such as users and boss machines and control network
- Malware from the outside world infecting experimental machines and spreading in DETER or propagating back to the outside (with implications of DETER unwittingly participating in attacks).

We have developed strategies to contain experiment risk while allowing users to observe phenomena of interest to them. This means that containment is customized for each experiment. This customization is performed automatically depending on information a user specifies on Begin Experiment Web page.

Please [file a ticket](https://trac.deterlab.net/newticket) (you must log in to Trac with your DETERLab username and password) if you run into any problems. This code is still being developed and we welcome your feedback.

## Who Is Eligible to Run Risky Experiments?

To be able to create and run risky experiments you must first request permission from DETERLab. Please [file a ticket](https://trac.deterlab.net/newticket) with your project name, a description of your experiment and its risk. We may need to exchange a few emails with you before granting permission to use this feature. Attempts to create risky experiments without approval of DETER staff will fail.

## Specifying Risk and Connectivity Options

This section explains how to specify risk and connectivity options and what effect this will have on your experiment. 

!!! note 
    Many containment strategies require automated modification of the experiment's NS file. We can currently only parse "simple" NS files, i.e. those that specify each node's features separately rather than using "foreach" option. We expect to extend our parsing ability in the near future. [parsable.ns​](/core/parsable.ns) is a sample file we can parse, and [unparsable.ns​](/core/unparsable.ns) is the same file in a version we *cannot* parse yet.

    Further, it is unclear yet how containment works with NS options such as traffic generation via NS file, use of templates, use of Planetlab nodes, etc.

### Configuring for Malware

This section describes which options to check for malware.

**Experiment uses live malware** If your experiment uses any type of malware such as DoS, worm code, exploit code, scanning code, etc. even if it is written by you, please check this box. You are not likely to experience any adverse effects if this box is checked. If you need no outside connectivity we may not take any special containment action, in addition to those already in place for all experiments. If you need outside connectivity we may log your traffic or filter it based on malware signatures to ensure no malware traffic escapes DETER. The type of containment will depend on your connectivity selections, and on type of malware you are using. Again, you are unlikely to experience any adverse effects from our actions.

**Malware self-propagates?** Check this box if you use malware such as worms that can spread by itself, once launched, through a set of vulnerable nodes without human action. Also check this box if you use email or other types of viruses that spread when a human accesses them through a benign application (e.g. human opens an email, human opens an IM attachment with the virus, etc). Checking this box will increase DETER's monitoring and filtering of traffic generated by your experiment to the control net and the outside.

**Type of malware.** This selection helps us customize our monitoring and filtering to a specific malware you are using. We expect this list to grow in the future. If no selection fits malware you are using, please select Unknown.

### Connectivity

**Experiment needs outside connectivity.** Check this box if you require the ability to initiate conversations with the Internet from your experiment. Connectivity will be provided by adding a special type of node, called tunnel to your topology. We will add this node automatically. Its name - tunnel - becomes a keyword so no other nodes in your topology can be named tunnel if this box is selected. The tunnel node will be linked to a random, well-connected node in your topology. We will also automatically set up routing on your nodes so they know they reach outside via the tunnel node. You will be able to see the added code in the NS file. Do not change it, otherwise you are likely to lose connectivity. You can change other parts of your NS file at will, including changing a link that connects the tunnel node with the topology to reconnect it to another experimental node. **Do not create additional links to the tunnel node, this likely won't work.** If at some point you do not need connectivity anymore, visit the Modify Experiment page and uncheck this box.

!!! note
    You will never get an unlimited outside connectivity. Instead we install a firewall on a tunnel node and open communication between your experiment and remote IPs you have specified on fine-grain basis. The rest of the form allows you to specify allowed communication patterns. If you don't know which nodes you will need to talk to at experiment creation time please file a ticket and explain to us your experiment design. We'll work together with you to find a safe way to support your experiment.

**Select type of communication with the outside.** If you plan to communicate with the Internet for benign purposes (e.g., to get rpms, to contact public Web servers in a benign way, to let others talk to a Web server you have set up in DETER) you would select the benign option. If you plan to let malware generate traffic to the outside or you plan to attract malware from the outside select Malware-generated. If you use multiple traffic generators that fall into different categories, select Mixed. Type of communication you select will have direct influence on the level of our monitoring and filtering on tunnel nodes.

**Names and ports of experiment nodes that receive outside connections.** If you plan to set up a server on some of your experimental nodes that will receive communication from the outside specify the nodes, destination ports and protocols here. Names of the nodes are names from your NS file. Only single protocols, not protocol ranges, can be specified. Supported protocols are tcp and udp. This communication is NAT-ed via the tunnel node, thus remote clients will need to contact you on a specific, high-numbered port. You will be notified of NAT specifics in an email once you swap your experiment in. This information can also be viewed on your experiment's Web page.

For example, let a node n1 in your topology have IP address 1.2.3.4 and wants to run a public Web server. You would specify n1/80/tcp in the textbox for experiment nodes that receive outside connections. NAT may map this into 5.6.7.8 port 10001. Remote clients would then type ​http://5.6.7.8:10001 to access your Web server. If this is confusing please file a ticket for more information.

**IPs and ports of outside nodes that receive connections from the experiment.** If you need to contact remote servers from your experimental nodes (e.g., to send some statistics to a server in your institution) specify the IPs, destination ports and protocols here. Same restrictions for ports (single numbers, not ranges) and protocols (tcp and udp) apply. Note that a lot of public servers have multiple IP addresses. If you want to contact such servers (e.g. Google) you will need to list all IP addresses to ensure that when you try to access them via their URL, the corresponding IP is on our list of allowed remote hosts. Otherwise you could list only one IP for such public servers and contact them using this IP address and not the URL (e.g., you could type 74.125.19.103 instead of www.google.com in your browser's address bar and this would guarantee that you always go to this one specific Google server).

## Logging on Tunnel Nodes

For security reasons, all prohibited traffic on tunnel nodes is logged. This means that traffic you specifically allowed via our forms for experiment creation and modification will not be logged but any other traffic to/from tunnel nodes and any other transit traffic will be logged.

## Accessing Tunnel Nodes

For security reasons, users do not have sudoer access to tunnel nodes.

## Rebooting and Modifying Experiments with Tunnel Nodes

You can safely reboot tunnel nodes if you want - they should come back up with all the correct settings. If you reboot any other nodes in your experiment you will need to set up routing to the outside. You can do this by logging on the rebooted nodes and running /share/t1t2/set_route if you have sudo privileges on that node. If you run into problems please contact us and we will help restore the connectivity.

The safest way to change something in your experiment is via Modify Experiment Web page. This should properly set up all the routes, firewall rules, etc. in your experiment. You will not be allowed to modify tunnel node's settings such as OS image and name but you can modify where this node links with your topology. If you need to add some start commands on experiment nodes (except the tunnel node) the best way is to copy the commands we have automatically inserted into your startup script, add commands you want, then replace the start-cmd options in the NS file with the name of your startup script. If you mess up startcmd options in the NS file routing on experiment nodes will not be set up properly. To amend the problem you can delete the entire "tunnelcode" section of the NS file, make sure that the connectivity box is checked and run Modify Experiment and the proper code for connectivity will be automatically inserted.