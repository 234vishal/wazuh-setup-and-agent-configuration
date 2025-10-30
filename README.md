# Wazuh-Setup-and-Agent-Configuration

![11_art22-00_7fbcbb2c](https://github.com/user-attachments/assets/287833b8-c1eb-493d-9ebd-f27b1e3e15dd)

This repository documents the complete setup and configuration of Wazuh SIEM on a Kali Linux virtual machine (VMware), including the installation of the Wazuh server, dashboard, and API. It also provides step-by-step guides for installing and configuring Wazuh agents on both Windows and Linux systems for endpoint monitoring.
The repository covers troubleshooting common issues like dashboard errors, blank API configuration files, and agent connection problems, along with verified commands, screenshots, and configuration examples for a smooth and secure deployment.

## Instaltion and Configuring Wazuh Server On Localhost
### Step-1:
- Download the `Open Virtual Appliance (OVA)` version of **Wazuh** for you VM Ware(vertual machine). Do read the specifications([Link](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html))
- Deploy the downloaded file in to your virtual machine, and make sure that you have allocated 8GB of RAM for you `Wazuh` server otherwise it will not run.
- **Screenshot:**
  
    -<img width="856" height="694" alt="wazuhvm" src="https://github.com/user-attachments/assets/5ecec7f3-8988-4701-911d-d99f63fdbdb4" />


### Step-2:
- Open the Wazuh in your virtual machine and login to your wazuh server using the given login password.
- **Screenshot:**

   <img width="652" height="145" alt="login" src="https://github.com/user-attachments/assets/6b7a67f8-67b2-4acc-ab3e-3480c39ae53a" />
   <img width="801" height="382" alt="wazuh" src="https://github.com/user-attachments/assets/ad3c9080-2eb8-4fae-b91b-9b29aef77f95" />



- Check the ip of the server by using command `ip a`

### Step-3:
- Now we have to configure the `Dashboard`, `Manager`, and `Indexer` for the `Wazuh` server.
- Use the below listed commands to start the Wazuh `Manager, Dashboard`, and `Indexer`
  
   ```
    sudo systemctl start wazuh-dashboard
    sudo systemctl start wazuh-manager
    sudo systemctl daemon-reload
    sudo systemctl start wazuh-indexer
   ```
- To check the status of `Manager, Dashboard`, and `Indexer` that if they are up and running use the below commands.

   ```
    sudo systemctl status wazuh-dashboard
    sudo systemctl status wazuh-manager
    sudo systemctl status wazuh-indexer
  ```

- **Screenshot:** You get an output for all the services something like this:-
  <img width="879" height="561" alt="manager" src="https://github.com/user-attachments/assets/8d111af9-baa6-498e-9f9c-6bab298edc82" />

- **Error:** If you are getting an error something shown in the below screenshot
  <img width="828" height="552" alt="error" src="https://github.com/user-attachments/assets/c1cf7e50-5b1f-4830-bc58-f4522a681a06" />
- Use the `free -h` and check that memory is allocated to `Swap` and if the memory allocated is zero then use the below listed command to allocate memory space to `Swap`

  ```
  # 1. Create a 2GB swap file
  sudo fallocate -l 2G /swapfile

  # 2. Secure the swap file
  sudo chmod 600 /swapfile

  # 3. Set up the swap space
  sudo mkswap /swapfile

  # 4. Enable the swap
  sudo swapon /swapfile

  # 5. Make it permanent across reboots
  echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

  # 6. Confirm that it is active
  free -h

  ```

- **Screemshot:** After this you will get something like this
  <img width="870" height="166" alt="Screenshot (129)" src="https://github.com/user-attachments/assets/6783a0a5-c3b2-4ab8-8a5e-860a57ce719b" />


### Step-4: 
- After starting `Dashboard,Manager and Indexer` and verfifying that all are up and running, open your browser and type `https://<Wazuh_IP>`.
  
  ```
  User-Name: admin
  Password: admin
  ```
- **Screenshot:** After login you will get a dashboard like this
  <img width="1863" height="890" alt="dashboard" src="https://github.com/user-attachments/assets/2a6fec77-a212-4ed0-96c3-c2dac6ccc815" />

- To access the wazuh server in your linux machine just use this command
  
  ```
  ssh wazuh-user@<wazuh_server_ip>
  # 1. Add the password that you used for login to wazuh server
  Password: wazuh
  ```

  ## Configuring Wazuh Aget On Windows
  - Download and Install the wazuh agent from the `Wazuh` site. ([Link](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html))
  - Now open the `ossec.conf` file path: `C:\Program Files (x86)\ossec-agent`  on notepad as an administrator and change the ip address with the current ip of the `Wazuh` server under <Client> </client> section.
    After that save the file and restart the `wazuh agent` services .
  - **Screenshot:**
       <img width="1029" height="729" alt="conffile" src="https://github.com/user-attachments/assets/07e0c443-6cb7-446f-8a77-28e13e66073e" />

  - Now go to the `wazuh server` go to the `Agent` section and then clink on to `Deploy new agent`
      - Now Select the package to download and install on your system: Windows 32/64 bit
      - Server address: wazuh server address
      - Optional settings: `Agent name`(what ever you want), `Select one or more existing groups`(Default)
      - Run the following commands to download and install the agent: you will get a command something like in the pic and run that code in your `powershell` as administrator 
        <img width="1805" height="240" alt="codW" src="https://github.com/user-attachments/assets/03837590-ce3c-4077-9850-f71ea5e5b35b" />
      - After that to start the agent run this command in the powershell
        ```
        NET START WazuhSvc
        ```
      - You will see that there will be an agent will get added to your wazuh dashboard
        
        <img width="1920" height="353" alt="Screenshot (149)" src="https://github.com/user-attachments/assets/0a6e18d0-3615-4322-a1db-422686c1c980" />
        
        <img width="1920" height="631" alt="Screenshot (148)" src="https://github.com/user-attachments/assets/22e7b680-71fd-42df-b22b-27741834dd37" />
        
 ## Configuring Wazuh Aget On Linux
   - The first step is to download the agent for your Linux system therefore open your linux terminal and run these commands.
     ```
     # 1️⃣ Add the Wazuh GPG key and repository
     curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo apt-key add -
     echo "deb https://packages.wazuh.com/4.x/apt stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

     # 2️⃣ Update your packages
     sudo apt update

     # 3️⃣ Install the Wazuh agent
     sudo apt install wazuh-agent -y
     ```
- Configure the agent file and change the ip address under server section as the current wazuh servers ip and save the file. and restart the agent.
  ```
  sudo nano /var/ossec/etc/ossec.conf

  # To restart the agent:
  sudo systemctl restart wazuh-agent

  ```
- After that go to the wazuh dashboard and deploy a new agent by selecting Linux.
  <img width="1920" height="655" alt="linux" src="https://github.com/user-attachments/assets/e1362a66-554e-4a31-88b7-3eaf799528c9" />
- Follow the same steps as you followed for the `Windows agent setup` then you will get the command , run that command in your `Linux` terminal.
<img width="1920" height="243" alt="Screenshot (145)" src="https://github.com/user-attachments/assets/1e441047-cc62-46dc-9f66-db5da2ac669e" />

- Now to start the agent run the following commands.
  ```
  systemctl daemon-reload
  systemctl enable wazuh-agent
  systemctl start wazuh-agent
  ```
- Wait for few minuts and then refresh you wazuh dashboard the linux machine will appear on you wazuh dashborad

## Conclusion
This repository provides a complete step-by-step guide for installing and configuring the Wazuh agent on Linux and connecting it to a Wazuh manager hosted on VMware. It simplifies the process of endpoint onboarding and ensures secure communication between the agent and the server.
By following these instructions, users can quickly set up a working Wazuh environment for monitoring system logs, detecting threats, and visualizing security events through the Wazuh dashboard.
This setup serves as a practical foundation for anyone starting with SIEM tools and host-based intrusion detection using Wazuh.


  




   

  
