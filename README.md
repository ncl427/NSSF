# Network-Slice-Selection-Function---NSSF
## Introduction
A Network Slice Selection Function is in charge of making the selection of an appropriate Network Slice in base of some parameters that a UE will send when it wants to attach to a Network. In the next sections the level of functionality achieved and the steps that are required for running this module will be described

## Description
The **NSSF** is part of the 3gpp solution for having E2E slicing in a Mobile Network. According to documentation,
the **NSSF** sits between the **RAN** and the **MME** and it is present mainly in the Attach and Detach procedures of a 5G mobile network.
According to **3gpp** documentation during Session Establishment the NSSF will be involved in Reselection of a Network Slice only if, the Network Configuration changed. Because of this is not part of the scope of our project, Slice reselection is not included in the functionality.

The current functionality present in the code is:
- Assign a Network Slice during Network Attach/Reattach procedure
- Erase UE slice assignation during the Network Detach procedure

It is important to make clear, the NSSF is not in charge of doing the Attach/Reattach procedure, it only passes the information received from **RAN** to the **MME**, and once the UE is allowed to have connection will assign the Network Slice based on the Type of Service that the UE requires.

### Functionality during UE Attach/Reattach procedure
The following Flow chart, shows the Attach/Reattach procedure of the NSSF

![ALT text](/Images/NSSF_Attach.png "Flowchart of the Network Slice Selection Function")

1. The NSSF receives from RAN the UE connection information, in our scenario it is contained inside an **Object**, the 2 main attributes required by the NSSF are UE Id (In our Scenario is the IP of the machine) and the Type of service.

2. Once the NSSF has the UE information, it verifies with its local Connection Database, if the UE has already being served. (The local connection database will have the information of the UE as long as this one haven't disconnected from the network).

  - **2.a** From this point 2 things can happen. If the UE information is not in the local Database, it means that we are working in the Attach Procedure, for which we need to forward the UE connection information that we receive to the MME to handle the connection, after doing this, the MME will reply with updated connection information (Required for the **Temporary Id**) or will not allow the connection (Based on its own policies)
  - **2.b** If the User equipment information was already inside the Local Connection Database it means that we are working with a Reattach procedure, for which we do not need to send any request to the MME and we proceed directly to the **Network Slice Selection**.

3. When the MME reply our attach request, we add in our Local Connection Database, the UE Id that is registered in the connection.

#### Network Slice Selection

4. The Network Slice Selection Function will query the Slices Database (Which will be generated by....) to find if a there is a Slice that can serve the Type of Service that the UE is requiring.

   - **4.a** If there is no Network Slice that can serve the UE, the NSSF will trigger a request to ..... for creating a Connection that can serve the UE **(This is not implemented in our scenario)** *If for some reason we reach this stage in our scenario, the connection is interrupted*  
   - **4.b** When there is a Slice with the right Type of Service, the **NSSF** will firstly, register the **UEId** Inside the Network Slice Databse and Register the **NSId** in the Local Connection Database, that way it is possible to know which **UE** is assigned to which **NS** while the connection is still active.

5. Once we reach this step, we need to complete the connection information that is going to be contained inside the **MDDVector** which is an object similar to the one that we received from the **RAN** at the beginning of the flow, but with the addition of a **NSId** plus a **Temporary ID** that contains the **UEId** plus other Core Network information received from the **MME** during the attach procedure.

6. Once we have the **MDDVector** we will reply it to the **RAN** ending the Attach/Reattach Request Procedure.
