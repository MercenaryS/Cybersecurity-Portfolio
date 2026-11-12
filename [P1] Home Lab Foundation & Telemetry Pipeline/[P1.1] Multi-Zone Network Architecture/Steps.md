This Steps will give you a little help configuring the firewall once ready with configured machines and selected ip range.

    1.Configure Network Interfaces and Static Subnets:
        Assign physical or virtual network interface cards (NICs) within pfSense to separate your WAN, LAN, and DMZ zones.
            - Log into the pfSense Web GUI using your gateway IP address (e.g., [https://x.x.x.x]).
            - Navigate to Interfaces > Assignments. Assign your physical or virtual NICs to LAN and OPT1 (rename OPT1 to DMZ).
            - Click on Interfaces > LAN, check Enable interface, set IPv4 Configuration Type to Static IPv4, and configure the IP address as x.x.x.x with subnet mask /xx (xxx.xxx.xxx.xxx). Save and apply changes.
            - Click on Interfaces > DMZ, check Enable interface, set IPv4 Configuration Type to Static IPv4, and configure the IP address as x.x.x.x with subnet mask /xx (xxx.xxx.xxx.xxx).
            - Save and apply changes
        
Verification: Navigate to Status > Interfaces in the pfSense menu and confirm that both LAN and DMZ show active status with their respective static IP assignments (x.x.x.x and x.x.x.x)
    
    2.Implement Stateful Zone Isolation and Lateral Movement Blocks.
        Create strict firewall rules to ensure hosts in the DMZ subnet cannot initiate traffic or lateral movement into the internal LAN.
            - Navigate to Firewall > Rules and select the DMZ tab.
            -Add a new blocking rule at the top of the ruleset:
                - Action: Block
                - Interface: DMZ
                - Address Family: IPv4
                - Protocol: Any
                - Source: DMZ net
                - Destination: LAN net
                - Description: Block DMZ to LAN Lateral Movement
        
        Add a subsequent pass rule underneath to allow DMZ outbound internet connectivity:
            - Action: Pass
            - Source: DMZ net
            - Destination: any (excluding LAN net)
            - Click Save and then Apply Changes.
        
Verification: Check the firewall rule order on the DMZ tab to ensure the explicit block rule for DMZ net to LAN net sits above any allow rules.

    3.Configure LAN Administrative Access to DMZ:
        Allow authorized administrators on the internal LAN to manage services hosted in the DMZ (such as Server-DMZ at x.x.x.x).
            - Navigate to Firewall > Rules and select the LAN tab.
                -Add a new pass rule:
                - Action: Pass
                -Interface: LAN
                - Source: LAN net
                - Destination: x.x.x.x (Apache-DMZ static IP)
                - Destination Port Range: SSH (22) / HTTPS(443)
                - Description: Allow LAN Management to Apache-DMZ.
                - ClickSave and Apply Changes.
        
Verification: Run a test connection or curl [http://x.x.x.x](http://x.x.x.x) from a LAN workstation to verify that administrative web traffic successfully reaches the DMZ server.