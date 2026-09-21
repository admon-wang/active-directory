# homelab1
 
This is the first project directory within the `active-directory` repository. Use this folder to store the Homelab 1 project files, configuration, documentation, and related resources.
 
## Overview
 
This project follows along with [*Installing Active Directory on Windows Server on Virtual Machine (Home Lab) (Ep. 1)*](https://www.youtube.com/watch?v=GsmJowwIh8Q) by East Charmer. The video walks through building a small Active Directory homelab on a VM: installing Windows Server 2022 in VMware Workstation Pro, promoting it to a domain controller, then creating Organizational Units (OUs), groups, and user accounts to simulate a basic corporate directory structure. The notes below capture the steps and concepts from that walkthrough.
 
---
 
## Creating Organizational Units (OUs)
 
<img width="746" height="497" alt="Active Directory Users and Computers window showing the right-click context menu on the domain node" src="https://github.com/user-attachments/assets/6cb9a367-8939-45c0-a255-496daaa7a50d" />

**Steps:**
 
1. Right-click Active Directory
2. New > Organizational Unit
3. Name the OU
**Create 3 example OUs (USA, Europe, Asia):**
 
<img width="747" height="525" alt="Active Directory Users and Computers showing USA, Europe, and Asia organizational units created under the domain" src="https://github.com/user-attachments/assets/45c9ca78-259b-4259-b6c9-6d9c585e2c5d" />
---
 
## Add These OUs to Each OU:
- Computer
- Users
- Servers

<img width="747" height="520" alt="Active Directory Users and Computers showing department sub-OUs for users, computers, and servers" src="https://github.com/user-attachments/assets/3e8bdb4a-c80a-4b46-b8f2-7f220895eb87" />
---
 
## Group Scopes and Group Types
 
**Three types of group scopes:**
 
1. **Universal** — Used to consolidate global groups that span **multiple domains** across the entire network forest.
2. **Global** — Used to organize users or computers from the **same domain** who share a similar business role or job function.
3. **Domain Local** — Used to grant access permissions to specific resources (like files, folders, or printers) located within a **single domain**.
**Two types of groups:**
 
1. **Security Group** — Used to assign user rights (what the user can do) or assign permissions (what the user can get into/access) to shared resources.
2. **Distribution Group** (a.k.a. Distro List) — Used to create email lists to send email to a collection of users.
---
 
## Creating Groups Within Each Department
 
**Steps:**
 
<img width="758" height="537" alt="Right-click menu on a department OU showing New > Group option" src="https://github.com/user-attachments/assets/a646a2cf-2aa8-41c3-8d7e-a42d988aef5a" />

- Right-click OU/department > New > Group

**Create Distribution Group:**
 
<img width="433" height="370" alt="New Object - Group dialog configured as a Distribution group" src="https://github.com/user-attachments/assets/a20b5f54-9cd2-4d0a-a46f-61254465b53b" />

**Create Security Group:**
 
<img width="433" height="376" alt="New Object - Group dialog configured as a Security group" src="https://github.com/user-attachments/assets/4d046215-0fba-4f49-aa6e-c16449d47561" />

---
 
## Creating a User
 
**Steps:**
 
<img width="771" height="592" alt="Right-click menu on a department OU showing New > User option" src="https://github.com/user-attachments/assets/327c6a67-0d20-4736-a355-d7fa8662ed2f" />

- Right-click OU/department > New > User

<img width="428" height="368" alt="New Object - User dialog for entering first name, last name, and logon name" src="https://github.com/user-attachments/assets/53aa3e4d-daee-484c-9d4a-cdf7f9d05411" />

- Enter details

<img width="425" height="375" alt="New Object - User dialog for setting the account password" src="https://github.com/user-attachments/assets/55cc3259-23e1-41c7-9aa7-a5c3805c541d" />

- Enter password

<img width="426" height="370" alt="New Object - User summary dialog with a Finish button" src="https://github.com/user-attachments/assets/8e5161e4-b3c4-4522-b127-354e8a35adab" />

- Click Finish

**Final Result:**
 
<img width="743" height="521" alt="Active Directory Users and Computers showing completed OU structure with users, groups, and computers" src="https://github.com/user-attachments/assets/dd07f0f4-9f22-4f7c-b221-fdaee2c199e4" />
