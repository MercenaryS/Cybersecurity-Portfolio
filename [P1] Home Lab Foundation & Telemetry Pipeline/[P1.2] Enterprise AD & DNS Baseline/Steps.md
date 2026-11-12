 configuring Enterprise Active Directory & DNS Baseline on Windows Server (Windows-DC at x.x.x.x)
   
    1.Configure Static IP Address and DNS Settings:
    Before installing Active Directory, the Domain Controller must have a static IP address and point to itself for DNS resolution.
        - Open the Start menu, type Control Panel, and press Enter.
        - Navigate to Network and Sharing Center > Change adapter settings
        - Right-click your network interface and select Properties.
        - Double-click Internet Protocol Version 4 (TCP/IPv4).
        - Select Use the following IP address and configure:
            - IP address: x.x.x.x  # Determined Ip Address & CIDR
            - Subnet mask: x.x.x.x  
            - Default gateway: x.x.x.x # pfsense Internal Lan Ip
        - Select Use the following DNS server addresses and configure:
                -Preferred DNS server: 127.0.0.1 (or WIN-AD-IP to reference itself locally for AD-integrated DNS).
        - Click OK and close the adapter properties.

Verification: Open Command Prompt and run ipconfig /all. Confirm the IPv4 Address is x.x.x.x and the DNS Servers point to designated DNS

    2.Install Active Directory Domain Services & DNS Roles:
        - Open Server Manager (it launches automatically upon login, or search for it in the Start menu).
        - Click on Add roles and features on the dashboard.
        - Click Next through the Before You Begin screen.
        - Select Role-based or feature-based installation and click Next.
        - Select your server (Windows-DC) from the server pool and click Next.
        - On the Server Roles list, check the box for Active Directory Domain Services. A popup will prompt you to add required management tools; click Add Features.
        - Also check the box for DNS Server and click Add Features for its management tools.
        - Click Next through the Features screen, read the AD DS and DNS overview notes, click Next, and then click Install.
        - Wait for the installation to finish, then click Close (you do not need to wait for promotion here).
        
Verification: In Server Manager, check the notification flag (yellow triangle) or ensure both AD DS and DNS icons appear in the left-hand navigation pane.

    3.Promote the Server to a Domain Controller.
        - Click the yellow warning flag notification at the top of Server Manager.- Click the link that says Promote this server to a domain controller. This opens the Active Directory Domain Services Configuration Wizard.
        - On the Deployment Configuration page:
            - Select Add a new forest.
            - Root domain name: Enter [Preferred Domain.Name].
            - Click Next.
        - On the Domain Controller Options page:
            - Leave Forest Functional Level and Domain Functional Level at default (Windows Server Threshold or newer).
            - Ensure Domain Name System (DNS) server and Global Catalog (GC) are checked.
            - Enter a secure Directory Services Restore Mode (DSRM) password and click Next.
            - On the DNS Options page, ignore any delegation warning and click Next.
            - On the Additional Options page, verify the NetBIOS domain name (DOMAINNAME) and click Next.
            - On the Paths page, verify the database, log files, and SYSVOL folder locations (C:\Windows\NTDS, C:\Windows\SYSVOL) and click Next.
            - Review your selections on the Review Options page and click Next.
            - Once prerequisite checks pass successfully, click Install.

Verification: The server will automatically restart upon completion. After rebooting, log back in with your domain administrator credentials and verify that system properties show the computer is now joined to domain.name

    4.Verify Forward and Reverse Lookup Zones:
        - Open Server Manager, click Tools, and select DNS.
        - Expand your server name in the left pane and select Forward Lookup Zones.
        - Confirm that cyberlab.local is listed, is Active Directory-integrated, and contains standard folders like _msdcs, DomainDcs, and your host record for HOST-NAME.
        - Expand Reverse Lookup Zones in the left pane:
            - If missing, right-click Reverse Lookup Zones > New Zone.
            - Select Primary zone, check Store the zone in Active Directory.
            - Select IPv4 Reverse Lookup Zone, enter Network ID x.x.x.x, and complete the wizard to create x.x.x.in-addr.arpa.Verify that a Pointer (PTR) record DC-IP to dc.domain.name exists.
            - Repeat Reverse Lookup zone for DMZ Network.
            
Verification: Open PowerShell on the server and run nslookup [Domain.name] and nslookup [x.x.x.x] to confirm both forward and reverse resolution respond successfully.

# Use different file name as per prefernece for [cyberlab] and for related files.

Deploying and Securing an Apache Web Server on Ubuntu
This walks through setting up a production-ready Apache web server on Ubuntu, including enabling necessary security modules, generating self-signed SSL certificates, configuring virtual hosts with automatic HTTP-to-HTTPS redirection, and establishing custom log file locations.
 
    Step 1: Install Apache
    Update your package index and install the Apache web server package.
        Bash
        sudo apt update
        sudo apt install apache2 -y
Verification: Run sudo systemctl status apache2 to ensure the service is active and running.

    Step 2: Enable Required Apache Modules
    Enable the modules necessary for SSL encryption, URL rewriting, and security header management.

        Bash
        sudo a2enmod ssl
        sudo a2enmod rewrite
        sudo a2enmod headers
Verification: Check the enabled modules directory by running ls /etc/apache2/mods-enabled/ and confirming ssl.load, rewrite.load, and headers.load are present.

    Step 3: Create the Web Document Root
    Create a dedicated directory to house your website's files and set appropriate ownership permissions.
        Bash
        sudo mkdir -p /var/www/cyberlab
        sudo chown -R $USER:$USER /var/www/cyberlab
    
    Create a simple test page to verify your site later:
        Bash
        echo "<h1>Welcome to CyberLab Secure Web Server</h1>" | sudo tee /var/www/cyberlab/index.html
Verification: Run ls -ld /var/www/cyberlab to verify the directory exists and has correct permissions.

    Step 4: Generate SSL/TLS Certificates
    Create a secure directory for your SSL keys and generate a self-signed certificate valid for your domain (e.g., web.cyberlab.local).

        Bash
        sudo mkdir -p /etc/ssl/cyberlab
        sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
            -keyout /etc/ssl/cyberlab/web.key \
            -out /etc/ssl/cyberlab/web.crt \
            -subj "/C=US/ST=State/L=City/O=CyberLab/CN=web.cyberlab.local"

    Secure the private key file so only root can access it:

        Bash
        sudo chmod 600 /etc/ssl/cyberlab/web.key
Verification: Run ls -l /etc/ssl/cyberlab/ to confirm both web.crt and web.key exist with restricted permissions.

    Step 5: Configure the Virtual Host
    Create a new Apache configuration file for your site in the sites-available directory:

        Bash
        sudo nano /etc/apache2/sites-available/cyberlab.conf

Paste the following configuration template (adjusting paths and domain names as needed):

Apache
<VirtualHost *:80>
    ServerName web.cyberlab.local
    DocumentRoot /var/www/cyberlab

    # Automatically redirect all HTTP traffic to HTTPS
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R=301,L]
</VirtualHost>

<VirtualHost *:443>
    ServerName web.cyberlab.local
    DocumentRoot /var/www/cyberlab

    SSLEngine on
    SSLCertificateFile /etc/ssl/cyberlab/web.crt
    SSLCertificateKeyFile /etc/ssl/cyberlab/web.key

    # Modern SSL/TLS Security Hardening
    SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite HIGH:!aNULL:!MD5

    # Security Headers
    Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"

    # Custom Log Paths
    CustomLog /var/log/apache2/cyberlab-access.log combined
    ErrorLog /var/log/apache2/cyberlab-error.log
</VirtualHost>
    Step 6: Enable the Site and Restart Apache
    Disable the default Apache welcome page, enable your new configuration site, test for syntax errors, and restart the service.

        Bash
        sudo a2dissite 000-default.conf
        sudo a2ensite cyberlab.conf
        sudo apache2ctl configtest
        sudo systemctl restart apache2
Verification: Ensure the configuration test returns Syntax OK and sudo systemctl status apache2 shows the service running cleanly.  