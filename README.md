# [CALL FLOWS](README.md)
## Cell Acquisition : physical broadband channel (PBCH)
- UE decodes the PBCH to get **MIB**(master info block)
- **MIB**contains information about
  - cell barred or not ?
  - system frame number **SFN**
  - **DM-RS configuration for PDSCH for SIB1**
  - **configuration and location of PDCCH** (CORESET0) 

## Cell Search : control resource set (CORESETs)
![Screenshot from 2023-11-25 12-29-16](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/ce76acb5-9f65-4bbf-ba93-31a8aa09d2c1)

## Cell Acquisition : System information block 1 (SIB1)
- SIB1 contains the remaining minimum system information
- this SIB1 is carried on the **PDSCH**
- **PDSCH** location on resource grid is in the **DCI**(downlink control information) and this **DCI** is carried in the **PDCCH** that is on the CORESET0
- after decoding SIB1 , UE gets
  - parameters for cell access/connection
  - location of other SIBs
  - PLMN(identity of mobile network whom this cell belongs to) , TAC(tracking area code where the UE is currently located) etc

![Screenshot from 2023-11-25 12-41-28](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/d3e93911-9f9a-4646-bf39-64acbe97f62a)

## Initial Access (RACH Procedure) 
- once the UE has decided which gNB it wants to register then it initiates that process by RACH procedure 

![Screenshot from 2023-11-25 15-47-17](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/66b6e6f5-24cb-4ba2-b694-6dab17d0ea13)

- (msg1) At the start of the initial process UE sends a preamble to gNB using **PRACH** (physical random access channel) .

- (msg2) then the cell responds with **PDCCH** and using **PDCCH** the cell assigns a **PDSCH** in the DL
- and on this**PDSCH** there is the response of the cell to the preamble or `random access response` and in this response the cell assigns
  - A temp **C-RNTI**
  - A UL scheduling grant
  - Timing advance , will enable the UE to send its transmissions at a time that its UL transmission is synchronized with the UL transmissions of the other users

- (msg3) Once the UE receives this message the UE responds using the **PUSCH**
- In this response , the UE requests a RRC setup connection and this request will also contain the **UE ID** (no collision)

- (msg4) When msg3 is received by the cell , it will again use **PDCCH** to assign **PDSCH**
- In this **PDSCH** there will be **RRC** connection setup message , means **RRC**(radio resource connection) can now be set up

## REGISTRATION PROCEDURE 

![Screenshot from 2023-11-05 09-53-31](https://github.com/KRIISHSHARMA/RESEARCH/assets/86760658/371e134e-832b-4966-abd9-b37309ea10ca)

- INITIAL REGISTRATION  : power on initiation
- PERIODIC REGISTRATION : to make sure device has not ran out of battery / broken
- MOBILITY REGISTRATION : if device moving to new location
- EMERGENCY REGISTRATION : where the device likes to access emergency calls

![Screenshot from 2023-11-05 10-00-50](https://github.com/KRIISHSHARMA/RESEARCH/assets/86760658/93a1aada-ff40-4d1b-96b2-a36fc1c105ca)

1. first step is registration request for eg(when device is turned on, mobile sends registration request to RAN)
2. RAN has to forward it to appropraite AMF but how will RAN know what is appropriate AMF
3. here there are 2 possilities
   1. first if the device has already been communicating to the 5G network then it will have a temp ID assigned (`5G-GUTI(5G global unique temporary identifier)assigned by AMF which is comprised of the GUAMI (Globally Unique AMF ID) and the 5G-TMSI (5G Temporary Mobile Subscriber Identity)., used when AMF region of UE is not known`) so it can use this to see which AMF the device was previously been in communication with and RAN can forward request to corresponding AMF
   2. if no previous identity available it means its the first power on procedure so RAN looks at the registration request and the registration request will have a slice identifier **NSSAI** , and then based on the slice identifier for this particular network slice the RAN sees which AMF supports that network slice and forwards the request to it (in case a Temp ID is not available, the NG-RAN uses the NSSAI provided by the UE at RRC connection establishment to select the appropriate AMF (the information is provided after MSG3 of the random access procedure))

4. once the request has been passed to the AMF we come to context transfer
5. `for example let us assume the registration request is because of mobility` so device has moved to a new region and trying to do a registration update ie. trying to connect to a new AMF but because it is mobility triggered the UE already have a UEcontext connection to old AMF
6. (2nd step)so in next step the NEW AMF contacts the OLD AMF for transferring the UE context `Namf_Communication_UEcontextTransfer` and the old AMF responds by transffering to the new AMF
7. (3rd step)in the next step for the to continue the authentication in carried out by 5G authentication and key agreement procedures(5G technology repo authentication procedure)
8. (4th step) in exapmle of new AMF taking over , the new AMF will confirm to the old AMF that it has recieved UEcontext and now can handle Access and mobility mgmnt from now on so the old AMF can retire
9. (5th step) the new AMF will register itself to the UDM saying it is the new AMF so take care of my valid info
10. and then it speaks to the UDM to get the necessary subscription information and also **subscribes** for any further updates to the subscription info in the future to know subscription status of device
11. on the other hand the OLD AMF has to retire for this purpose the UDM will notify the old AMF that it needs to deregister , so the OLD AMF will unsubscibe to any subsciption data that it has previously subscibed to
12. so now the tranfer to new AMF is complete and OLD AMF retires
13. (6th step) next the AMF needs to know the necessary policies related to access and mobility management so it goes to the PCF and fetches the necessary policy info corresponding to the device undergoing registration and the PCF provides the corresponding response
14. (7th step) next if there is already an existing PDU session then the AMF speaks to the SMF to continue the PDU session using the `UpdateSMcontext` message on the other hand if there is some kind of  missmatch in the understanding of what the PDU session state is then previous PDU session is terminated using the `ReleaseSMcontext` message and new PDU session created if necessary
15. (8th step) if the registarion process is going smooth so far the AMF will notify the device that the registraion has been accepted and optionally the device responds with confirmation message
16. (9th step) the AMF can contact the PCF for any device specific policies for eg device might have to decide that for a specific application if it has to create a new PDU session or not and if there are multiple wifi networks are supported which one should the device prioritize to use in support of 5G network
17. these types of policies can be fetched by the PCF if necessary 

## 5G AKA procedre 
![Screenshot from 2023-11-14 17-15-55](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/9835d5d3-cfed-400e-bd67-62e228b5fe06)

- once the authentication procedure has been initiated then UDM has the SUPI and based on the SUPI , UDM decides whether it has to use 5G AKA procedure or EAP-AKA procedure(we are looking at AKA)
- the ARPF generates the 5G home environment authentication vector cause (AUSF and UDM are in hone network)
- the expected response to the random number(XRES) is calculated using the MILENAGE FUNCTION and the input to the milenage function are sequence number , random number , Authentication manaegement field and the master key (K)
-  this milenage function has 5 subfunctions :- `MAC(generates message authentication code)` , XRES(response to random number) , IK , AK

![Screenshot from 2023-11-14 17-19-30](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/cb0f8115-0224-4812-9db3-d8f82ffde458)

- UDM derives XRES* as follows using HMAC-SHA-256 KDF function

![Screenshot from 2023-11-14 17-23-22](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/d575c440-f60f-4a19-b030-eb33b20293f7)

- UDM derives K_AUSF as follows using HMAC-SHA-256(K,S) `KDF(key derivation function)` as below 

![Screenshot from 2023-11-14 17-25-37](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/0e331afc-0f54-4ce0-86f9-451cd643bb51)

- AUSF derives K_SEAF from K_AUSF by passing K=K_AUSF and S= 0x6C || serving network name || length of serving network name to KDF function
- this K_SEAF for the time being is stored in the AUSF 

![Screenshot from 2023-11-14 17-27-52](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/60437617-72c2-4918-833d-b002802d7db9)

- AUSF uses the XRES* to calculate the HXRES*
- HXRES* calculation at AUSF : HXRES* is 128bit MSB of the output of SHA-256 hash, calculated by passing RAND||XRES* as input to SHA-256 algorithm 

![Screenshot from 2023-11-14 17-30-35](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/65a20eaa-302c-41c4-b129-7f8a0b9a4d9e)

- AUSF sends the 5G SE AV (serving environment authentication vector) to the AMF (SE cause AMF is located in the serving network of the UE)
- SEAF(AMF) stores the HXRES* and send random number (RAND) and authentication token (AUTN) tothe ME (mobile equipment) which send it to USIM

- USIM verifies the AUTN and derives the RES and keys(CK,IK)

- UE uses milenage function to derive XMAC , RES , CK , IK as below

![Screenshot from 2023-11-14 17-36-38](https://github.com/KRIISHSHARMA/5G_TECHNOLOGY_ARCHITECTURE_AND_PROTOCOLS/assets/86760658/bef02ddb-037a-4dbb-8e5f-39a2c0260696)

- if the XMAC and MAC are the same then it means its the genuine mobie network that this UE is connecting
- also verifies that sequence number(SQN) is in the correct range

- CK and IK are sent to ME and it calculates RES* and derive Kausf key , Kmac key , Kamf key
- then ME send RES* and MAC to the AMF(SEAF)

- SEAF(AMF) calculates the HRES* from RES* and compares it with HXRES* from the HXRES* it hase already stored if they are the same it means it is good
- AUSF compares RES* with XRES* (AUSF stores XRES*) if it is the same then authentication is succes


## PDU SESSION ESTABLISHMENT

![Screenshot from 2023-11-05 12-57-14](https://github.com/KRIISHSHARMA/RESEARCH/assets/86760658/0e7d6f6f-c8e7-4c6e-a36f-f0a2bd7ee377)

![Screenshot from 2023-11-05 12-57-52](https://github.com/KRIISHSHARMA/RESEARCH/assets/86760658/fb8a6462-56d7-4daa-b156-2893c83c25b6)

- (1st step) device sends the session establishing request to RAN which is forwarded to the AMF
- this request contains information like which data network this device is requesting a PDU session and what type of session type is it looking for (IP based or ethernet or unstrucured) based on info like this there is one more necessary information , is this a new PDU session or a handover
  - in case of new PDU session the AMF will see what is the type of session that the device has requested , what data network is it trying to connect to , for eg if trying to connect to IMS(IP multimedia subsystem(call)) than the AMF will select an apropriate SMF to do the session handling on the other hand if it is connectivity towards internet then the AMF will select a corresponding SMF for doing the session managemnt
  - in case of session handover its not a new session so AMF can look into the device context and the context will already be associated with particular SMF so it can contact the corresponding SMF in that case

- (2nd step) the AMF contacts the SMF for creating the session manangment context and in addition contact the UDM to get the necessary subscription data and subscribes to further  updates of the subscription data
- when the SMF agrees to the session managment context creation it provides the corresponding positive acknowledment as the response

- (3rd step) if authentication necessary that is taken care in this step

- (4th step) now SMF selects an apropriate PCF and creates a policy session so it can get the necessary PCC(The Policy and charging control rule (PCC rule) comprises the information that is required to enable the user plane detection of, the policy control and proper charging for a service data flow) rules from the PCF
- and also it selects an apropriate UPF to connect to the correct data network and creates a connectivity with the corresponding UPF
- **At this point SMF selects an IP address for the device**

- (5th step) at this point SMF sends an accept message towards the device together with info relateded to QoS and also the tunnel between the gNB and the UPF
- this message is sent via AMF which then forwards to RAN

- (6th step) at this point the QoS and tunnel info has reached the RAN which then creates connectivity towards the device and replies back to the AMF using the tunneling information at the end point of the RAN this means the setup is now complete for sending UL data from the device towards 5G network
- first UL data can now be sent

- (7th step) in this stage the AMF has previously received the RAN end of tunnel information from the RAN so it forwards this to SMF and SMF forwards to UPF
- so that the UPF know what is tunnel end point in the RAN end so it establishes connectivity from UPF to RAN
- so the UPF can forward the first DL data from data network towards device

- (8th step) now that UL and DL data in successful the AMF can go register itself to the UDM that this is the corresponding to the PDU session that was currently created 

## DEREGISTRATION PROCEDURE 

![Screenshot from 2023-11-05 10-10-37](https://github.com/KRIISHSHARMA/RESEARCH/assets/86760658/65d6c0dd-f0c3-4a60-9d1b-5cffbb5f92f8)

- (1st step) device forwards the deregistration request to the AMF by a RAN after recieving the request
- (2nd step) AMF starts to tear down any session related context so
- it first contacts the SMF to release the UE context associated with the ongoning PDU session
- SMF recieves the request and asks the UPF to release any user plan  resources related to this PDU session
- SMF forwards info backto AMF saying the user plane are now released meaning the UE context is also released
- based on this SMF also notifies the PCF that it no longer requires any policy associations that are associated with that PDU session and it also unsubscribes to the UDM that it no longer need any updates that it had previously requested from it
- SMF also deregisters with the UDM that this PDU session is no longer valid
- (3rd,4th step) the AMF notifies the PCF that the policy association related access management policies are no longer needed
- (5th step) once the PDU session is turned down and all the policy associations hav been disconnected then the AMF confirms to the device the deregistration is accepted
- (6th step)  AMF notifies RAN to release the Radio access related connection information 


- UE then ACK it using **PUCCH**
- using **RRC** connection the UE registers itself with the cell
