Splunk Virtual Machine Setup Instructions & Lab Notes

Objective

The objective of this phase is to prepare the Ubuntu Server virtual machine that will host Splunk Enterprise for our Active Directory monitoring lab. This involves configuring static networking, verifying external internet access, handling package manager locks, and mounting a shared host folder to access the Splunk installation package.

1. Network Configuration (Netplan)

Initial State: Wrong IP Address

<img width="982" height="323" alt="project2" src="https://github.com/user-attachments/assets/337784b4-deef-42fc-b419-c146eb8e8405" />

Why was it wrong?

By default, Ubuntu Server receives a dynamic IP address via DHCP assigned by the hypervisor's default virtual network (often on an arbitrary subnet like 192.168.something or 10.0.2.x).

For our Active Directory lab topology, the Splunk SIEM needs a predictable, static IP address (192.168.10.10/24) so our Domain Controller, targets, and Universal Forwarders know exactly where to ship logs without worrying about DHCP lease expirations changing the address.

How to Configure it Correctly

We modify our Netplan configuration file (/etc/netplan/00-installer-config.yaml or similar):

<img width="433" height="210" alt="image" src="https://github.com/user-attachments/assets/b8f58190-582d-43b1-9707-3c781af579da" />

Apply the changes:

<img width="881" height="285" alt="project3" src="https://github.com/user-attachments/assets/2398cacc-0199-4b1a-af9f-5262e900dfc0" />

How is this correct?

- The network adapter interface now explicitly displays the assigned static IP 192.168.10.10/24 with scope global.

- DHCP is disabled (dhcp4: no), ensuring the server retains this address across reboots.

- DNS resolvers (nameservers: [8.8.8.8, 1.1.1.1]) are explicitly defined so domain names can be resolve

__________________________________________________________________________--

Troubleshooting

In order to verify that our splunk machine is setup, we should ping an external site like google.com

<img width="450" height="42" alt="image" src="https://github.com/user-attachments/assets/90cce9a7-8af8-4d67-92ab-b613a43e7d83" />

>Photo that shows ping works


_____________________________________________________________________________

Creating Shared Folder:



_____________________________________________________________________________


Configuring __

<img width="352" height="52" alt="image" src="https://github.com/user-attachments/assets/68b8d614-3587-48d9-bc4c-8ca18dc78894" />

Install virtualbox-guest-utils:

<img width="987" height="562" alt="image" src="https://github.com/user-attachments/assets/f66075cb-29b6-4550-8896-bed4d0784594" />


Now we can add our user 'mydfir' to our group called 'vboxsf'

<img width="440" height="23" alt="image" src="https://github.com/user-attachments/assets/40e9420f-a982-4a75-a762-05e73f09f78d" />


Create New Directory called 'Share'

<img width="518" height="242" alt="image" src="https://github.com/user-attachments/assets/dc590a8e-9a16-4f1e-bbcc-79c1ef35d82a" />


Now let's mount our shared folder to our new directory called 'Share'



