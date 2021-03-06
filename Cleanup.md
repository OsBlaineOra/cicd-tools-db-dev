# Cleanup

## SSH Keys (Optional)

I recommend that you keep your ssh keys to use with future projects.  
If you choose to keep them, skip this section.

Run the following in your **Cloud Shell**

1. Remove the compute instance from your Known Hosts

   ```bash
   ssh-keygen -R ${COMPUTE_IP}
   ```

1. Delete your Private Key  

   ```bash
   rm ~/.ssh/id_rsa
   ```

1. Delete your Public Key

   ```bash
   rm ~/.ssh/id_rsa.pub
   ```

## Database

1. Open the menu
1. Click "Autonomous Transaction Processing"  
   ![Autonomous Transaction Processing Menu Item](images/Cleanup-OpenDB1.png)  
1. Locate your database
1. Click the three dots menu
1. Click Terminate  
   ![Terminate](images/Cleanup-TerminateDB1.png)  
1. Enter your Database Name in the box
1. Click Terminate  
   ![Confirm Terminate](images/Cleanup-TerminateDB2.png)  

## Network Ports

1. Close ports 8080 and 8000 in your cloud Virtual Network (VNIC).
   1. Click "Public Subnet"
      ![Open Subnet](images/openPort1.png)
   1. Click the Security List name.  
      ![Open Security List](images/openPort2.png)  
   1. Check the box next to port 8080 and 8000.
   1. Click "Remove".  
      ![Remove](images/Cleanup-Security1.png)
   1. Click "Remove".  
      ![Remove](images/Cleanup-Security2.png)  

## Compute Instance

1. Open the menu
1. Click "Compute"  
   ![Compute Menu Item](images/Cleanup-OpenCompute1.png)  
1. Locate your Compute Instance
1. Click the three dots menu
1. Click Terminate  
   ![Terminate](images/Cleanup-TerminateCompute1.png)  
1. Check the box "PERMANENTLY DELETE THE ATTACHED BOOT VOLUME"
1. Click Terminate  
   ![Confirm Terminate](images/Cleanup-TerminateCompute2.png)  

[Resources](index.html?lab=resources)
