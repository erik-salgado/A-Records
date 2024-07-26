
![dns-icon5739](https://github.com/user-attachments/assets/00ed6fdc-be34-4728-9c93-e9e94ccc0e6e)

# Understanding DNS

In this tutorial we will be inspecting DNS A-Records (hostname to IP address mappings) and create some of our own A-Records on the server and observe them.

# Requirements for this Lab
- Active Directory running in Azure on a virtual machine (DC-1)
- A client machine running in Azure on a virtual machine (Client-1) and joined to the domain

# Steps

- Go to DC-1 as your domain account (mydomain.com\jane_admin)
![Screenshot 2024-07-07 184904](https://github.com/user-attachments/assets/a18054f7-2d18-4238-b543-c865a3b910d3)

- Log into Client-1 as your admin account (mydomain.com\jane_admin) and try to ping "mainframe." ("mainframe" is just a random name) Notice that it failed.
![Screenshot 2024-07-09 200956](https://github.com/user-attachments/assets/8290f0d8-6614-42ab-a447-332ba6594e54)

So this is what haapens in the background when client-1 is pinging "mainframe". It will search first the local DNS cache to see if it is there. If there is nothing in the local DNS cache, it will then go and check the host file. The host file is in all Windows computers, its inside of (C:\windows\systems32\drivers\etc\hosts). The host file has a list of mappings or IP Addresses but usually by default the host file is empty. So after it checks the host file and finds nothing there, it will then go to the DNS server, which is assigned to the network interface card or the NIC, and then when nothing is there it will just failed.

![image](https://github.com/user-attachments/assets/1b8d8043-3d78-4d92-b561-2849c56014c1)
![image](https://github.com/user-attachments/assets/efd2bd32-0698-4511-9173-2b7fdd87ae18)

- Create A-Record "Mainframe". Go to DC-1 and open server manager > tools > select DNS
![Screenshot 2024-07-09 201345](https://github.com/user-attachments/assets/1d101651-7016-4d38-8fe7-7dca77b87577)

- Click on the forward lookup zones (Forward Lookup Zones means host name to IP address mapping. Reverse Lookup Zones means IP address to host name mappings.) Go to your domain name. Notice that we have a list of A-Records, we have client-1 and the mapping, as well as DC-1. This got registered over the network, when client-1 came online, DNS got integrated in Active Directory. So we are going to manually create another A-Record. Right click and select New Host 
![Screenshot 2024-07-09 201713](https://github.com/user-attachments/assets/96f8d8d0-1d33-4fe9-a317-aa405de8c34b)

- Type "mainframe" and IP address you can choose whatever you want and then click add host. I chosed to use 10.0.0.4 as the IP address.

![Screenshot 2024-07-09 201837](https://github.com/user-attachments/assets/0cdfb250-32c7-4c65-9912-a2e7815b8242)
![Screenshot 2024-07-09 201846](https://github.com/user-attachments/assets/592ccb00-8742-42c1-9949-be516c50de8f)

- Go to Client-1 and ping "mainframe" Notice it will ping successfully as a result of creating a A-Record "mainframe"
![Screenshot 2024-07-09 202640](https://github.com/user-attachments/assets/8f20d354-9af6-48c2-b9fe-9045ab9f74c4)

- Type "nslookup mainframe" and you'll see the first server DC-1 with the IP address and the record mainframe we've created
![Screenshot 2024-07-09 202037](https://github.com/user-attachments/assets/d2272729-eab7-4452-89cf-b9aacfd03b60)

- Next we'll look at the DNS cache on client-1. Type "ipconfig /displaydns" There will be a lot of records. Since its been resolved already here in the local cache, it will be much easier for the computer to find it instead of going directly to the DNS server.

![Screenshot 2024-07-09 202326](https://github.com/user-attachments/assets/26500048-5268-4275-baea-b06759cfd14f)

# Local DND Cache Exercise

- Go to DC-1 anc change mainframe's record address to 8.8.8.8.
![Screenshot 2024-07-09 202530](https://github.com/user-attachments/assets/c6c16fee-d0ea-4b9c-9a31-c6957f253510)
![Screenshot 2024-07-09 202539](https://github.com/user-attachments/assets/6685b3dc-025d-4c77-80f9-fd8a455fcab4)

- Go back to Client-1 and ping "mainframe". Notice that even though we've changed the IP address to 8.8.8.8, its still pinging the old address. The reason for this is because this still exists in the local DNS cache on this computer.
![Screenshot 2024-07-09 202640](https://github.com/user-attachments/assets/34643200-4f5c-4755-a22a-d1316dbe4210)

- If you type "nslookup mainframe" it will have the same old IP address
![Screenshot 2024-07-09 202746](https://github.com/user-attachments/assets/b39c3c5c-b801-4a40-9d6b-bc04de4c46f4)

- This is where the command DNS FLUSH comes into play. Sometimes a network resource IP address might change, but your local computer still has the old IP address cached and this could cause you to not reach out certain resources. So oftentime in troubleshooting we use the command dns flush to wipe out all of the cache and lets it get repopulated by a querying DNS server. You need to open command prompt as an admin in order to do this, type "ipconfig /flushdns"

![Screenshot 2024-07-09 202846](https://github.com/user-attachments/assets/6703bdaa-f209-4309-9728-50fbbfbbe9ec)
![Screenshot 2024-07-09 203045](https://github.com/user-attachments/assets/2ce5b5a6-ef5d-4509-a607-ca2640dd89d0)

- Type "ipconfig /displaydns" command and notice that it's empty, thats becasue we flush all the dns that was there.
![Screenshot 2024-07-09 203205](https://github.com/user-attachments/assets/9e475f86-7848-4adf-b6c6-300c3573add3)

- Ping "mainframe". So now this is what's happening in the background. The computer will search the local DNS cache but it wll be empty because of the flush dns command, it will then go to the DNS server, and since we've updated the records on the DNS server, it will put the latest record. So after pinging "mainframe" it will resolve it to 8.8.8.8
![Screenshot 2024-07-09 203223](https://github.com/user-attachments/assets/71e560e0-a8ce-43dc-8a91-89b5841bd4ad)

- If you type "ipconfig/ displaydns" it will show the latest record there is
![Screenshot 2024-07-09 203244](https://github.com/user-attachments/assets/af72fd97-4484-4053-9fc3-0f83a14bde15)














 








