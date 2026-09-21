# homelab1

This is the first project directory within the `active-directory` repository.

Use this folder to store the Homelab 1 project files, configuration, documentation, and related resources.

_____________________________________________________________________________________________________________

Creating Organizational Units (OUs)

<img width="746" height="497" alt="image" src="https://github.com/user-attachments/assets/6cb9a367-8939-45c0-a255-496daaa7a50d" />

Steps:
1. Right-click Active Directory
2. New > Organizational Unit
3. Name the OU

Create 3 Example OUs (USA, Europe, Asia):
<img width="747" height="525" alt="image" src="https://github.com/user-attachments/assets/45c9ca78-259b-4259-b6c9-6d9c585e2c5d" />

_____________________________________________________________________________________________________________

Add user accounts and groups to these OUs

- Users (Add 3 people for each Department):
  - IT
  - Consulting
  - HR
  - Sales
  - Management

- Computers:
  - IT
  - Accounting
  - HR
  - Sales
  - Management

- Servers

<img width="747" height="520" alt="image" src="https://github.com/user-attachments/assets/3e8bdb4a-c80a-4b46-b8f2-7f220895eb87" />

_____________________________________________________________________________________________________________

Three Types of Group Scopes:
1. Universal - Used to consolidate global groups that span **multiple domains** across the entire network forest.
2. Global - Used to organize users or computers from the **same domain** who share a similar business role or job function.
3. Domain - Used to grant access permissions to specific resources (like files, folders, or printers) located within a **single domain**.

Two Types of Groups:
1. Security Group - Used to assign user rights (what the user can do) or assign permissions (what the user can get into/access) to shared resources.
2. Distribution Group (a.k.a. Distro List) - Used to create email lists to send email to collection of users 

_____________________________________________________________________________________________________________

Creating Groups within each department

Steps:
<img width="758" height="537" alt="image" src="https://github.com/user-attachments/assets/a646a2cf-2aa8-41c3-8d7e-a42d988aef5a" />
- Right-click OU/department > New > Group

Create Distribution Group

<img width="433" height="370" alt="image" src="https://github.com/user-attachments/assets/a20b5f54-9cd2-4d0a-a46f-61254465b53b" />

Create Security Group

<img width="433" height="376" alt="image" src="https://github.com/user-attachments/assets/4d046215-0fba-4f49-aa6e-c16449d47561" />

Create User

Steps:
<img width="771" height="592" alt="image" src="https://github.com/user-attachments/assets/327c6a67-0d20-4736-a355-d7fa8662ed2f" />
- Right-click OU/department > New > User

<img width="428" height="368" alt="image" src="https://github.com/user-attachments/assets/53aa3e4d-daee-484c-9d4a-cdf7f9d05411" />
- Enter details

<img width="425" height="375" alt="image" src="https://github.com/user-attachments/assets/55cc3259-23e1-41c7-9aa7-a5c3805c541d" />
- Enter Password

<img width="426" height="370" alt="image" src="https://github.com/user-attachments/assets/8e5161e4-b3c4-4522-b127-354e8a35adab" />
- Click Finish

Final Result
<img width="743" height="521" alt="image" src="https://github.com/user-attachments/assets/dd07f0f4-9f22-4f7c-b221-fdaee2c199e4" />
