ReadMe

- In this file it is mentioned how I considered the Computational Resources / Network and Ip Assignment / Upgrade and Scalability of the Lab Architecture. 
- To Determine my lab setup, I had to consider my computational resources.

- As I am using my laptop with config
	- CPU : 8 cores / 16 logical processors
	- RAM : 40GB DDR5
	- SSD : 1TB

- After considering those I concluded with setting up 7 VMs
	- VM1 : pfsense firewall (OSS)
	- VM2 : Ubuntu-Server (Apache2)
	- VM3 : Win-Server 2019 As (ADDS / DNS)
	- VM4 : Win-Endpoint-01
	- VM5 : Win-Endpoint-02
	- VM6 : Linux-Endpoint (Ubuntu)
	- VM7 : Ubuntu-Server (Siem)

- I used Hyper-V as it is Type1 hypervisor and available on my host laptop.
	- Refer "Take-a-peek\Computation.png"
	- Did CPU, RAM, Storage allocation according VM use and purpose.


- After Configuring all The VMs, Selected a 2 Class A IP addresses 10.x.x.x with CIDR /24 to have Number of Class B Hosts.

- Used Pfsense Open Source firewall to Establish the zoning and Segmentation by using Hyper-V switch feature.
	- Internal Lan
	- DMZ lan
	- Refer "Take-a-peek\Lab-Arch.drawio"

- Configured all the VMs:
	- Installed pfsense-firewall and Establish isolation between Internal-LAN (10.10.10.0/24) and DMZ (10.10.20.0/24) using pfSense(LAN) (10.10.10.1) and pfSense(DMZ) (10.10.20.1).
		- Implemented strict stateful firewall rules blocking DMZ-to-LAN lateral movement while permitting administrative access from LAN to DMZ (10.10.20.x).
		- Refer :
			->"Take-a-peek\Fw-LAN-2-DMZ-Admin-Rules.png"
			->"Take-a-peek\Fw-DMZ-2-LAN-Block-Rules.png"
	- Installed all other Operating Systems and configured them with designated IPs, (10.10.10.x) for all Internal-LAN resources and (10.10.20.x) for any DMZ resources.

- With this our first project is complete Which consisted all the Networking Related Tasks.
- And with this project I understood How the networks are segmented and works in real-Scenario.
- I also captured the snapshots/checkpoints to Build this lab later with individual Machine and scale it by adding few more machines.
- I hope in future I will have enough fund to start my own lab once I settle down.
- Until then any body can follow this Arch to build their own lab.
- If you have any question/suggestion or want to discuss anything with me please feel free to reach out to me at "smuqtaruddin@gmail.com" 

