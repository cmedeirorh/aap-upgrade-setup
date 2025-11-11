# Ansible Automation Platform - Upgrade Workshop Prep

This automation deploys the necessary infrastructure to deploy AAP 2.4 and then upgrade it to AAP 2.5/2.6 (on AWS/EC2)

Always use the official documentation for reference: [Upgrade to the latest version of Ansible Automation Platform](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/rpm_upgrade/index).

## Using this Project
### Pre-Requisites:
* AAP instance to run the automation. This has been tested with AAP versions 2.5 and 2.6.
* AWS Blank Open Environment. This has been tested with the one from demo.redhat.com.
* Red Hat AAP valid subscription and permissions to access.redhat.com

### Step 1 - Request an AWS Blank Open Environment on RHPDS (demo.redhat.com)
Request an [AWS Blank Open Environment](https://catalog.demo.redhat.com/catalog?item=babylon-catalog-prod/sandboxes-gpte.sandbox-open.prod) and wait for it to be fully provisioned and ready. Once it is ready, identify the credentials provided under the services tab. Make note of the "AWS_ACCESS_KEY_ID" and "AWS_SECRET_ACCESS_KEY".

### Step 2 - Configure the AAP instance to run the initial automation
#### Create a Credential for AWS
* Log in to the Platform with an admin credential, and under the Menu **Automation Execution**, expand **Infrastructure** and select **Credentials**
* Under **Credentials**, select **Create credential**.
  <img width="1493" height="718" alt="Screenshot 2025-11-11 at 1 04 50 PM" src="https://github.com/user-attachments/assets/e52ca6c1-00c6-4049-a2d3-33b901f1599d" />
* Create a credential for AWS with the details noted in Step 1
  <img width="1630" height="633" alt="image" src="https://github.com/user-attachments/assets/528684f3-1580-4466-8796-db090725ca7e" />

#### Also under **Infrastructure**, select **Inventories** and Create (or use an existing one)
  <img width="1339" height="683" alt="Screenshot 2025-11-11 at 1 12 16 PM" src="https://github.com/user-attachments/assets/feefef56-a262-4992-9532-60bd2e6613a7" />
  <img width="1885" height="931" alt="Screenshot 2025-11-11 at 1 16 19 PM" src="https://github.com/user-attachments/assets/ae0bb71a-e721-4ac5-8539-fc3afdf49a28" />

* Add a Source to create the inventory dynamically using the AWS plugin
  <img width="1820" height="768" alt="Screenshot 2025-11-11 at 1 20 03 PM" src="https://github.com/user-attachments/assets/c33eca0a-8cba-49aa-ac90-453b61b509d8" />
  <img width="1886" height="933" alt="Screenshot 2025-11-11 at 1 23 32 PM" src="https://github.com/user-attachments/assets/76fb5d20-19b5-43c6-ac17-032e6cace60a" />
  Use these variables for the plugin: [aws_inventory_source_variables](https://github.com/cmedeirorh/aap-upgrade-setup/blob/main/aws_inventory_source_variables.yml)
  
#### Create a **Project** to sync the automation code
* Under **Projects**, create a new project with this Git repository: https://github.com/cmedeirorh/aap-upgrade-setup
  <img width="1895" height="770" alt="Screenshot 2025-11-11 at 1 31 56 PM" src="https://github.com/user-attachments/assets/924f353d-cff2-4a79-be77-eb75b8824804" />
  <img width="1871" height="926" alt="Screenshot 2025-11-11 at 1 32 29 PM" src="https://github.com/user-attachments/assets/25c33d5a-9c5b-414d-97c2-21a696968fc4" />

#### Create **Templates** to run the automation
* Under **Templates**, create a new **Job Template**
<img width="1901" height="490" alt="Screenshot 2025-11-11 at 1 35 45 PM" src="https://github.com/user-attachments/assets/9b0d1911-548d-45c6-adef-fe246be76c81" />

* First **Job Template** will create the AWS infrastructure and the VMs
  <img width="1886" height="934" alt="Screenshot 2025-11-11 at 1 44 58 PM" src="https://github.com/user-attachments/assets/7f0afdae-6689-430e-a289-a22a3a204683" />
  A variable called "aws_public_key" must be provided; the value must be a valid public SSH key that will be used to access the VMs once provisioned.

* Run the **Job Template** and verify that the VMs were created in AWS.
* Go back to the **Inventory** > **Sources**, and sync it. Check if the VMs are added to the **Inventory**
  <img width="1894" height="531" alt="Screenshot 2025-11-11 at 1 56 40 PM" src="https://github.com/user-attachments/assets/7d6e6be8-f589-4b3b-89b5-d069c19df5ae" />
* Switch to the **Hosts** tab and verify if the VMs are available.
  <img width="1883" height="827" alt="Screenshot 2025-11-11 at 1 59 25 PM" src="https://github.com/user-attachments/assets/bae8b8b3-3f2e-4ecc-be89-54367be6ef5b" />

* Create a second **Job Template**, which will prepare the nodes and copy the required files to the "bastion" node
  <img width="1891" height="946" alt="Screenshot 2025-11-11 at 2 53 08 PM" src="https://github.com/user-attachments/assets/ee25b5eb-b3e7-4f08-a8c6-d5fa661f06d9" />
  The following **Extra variables** are required (the values are examples only):
  ```
  aap_password: ThisWorkshopIs!Awesome
  inv24_dest: /aap/ansible-automation-platform-setup-2.4-13/inventory
  rhn_activationkey: KEY
  rhn_orgid: ORGID
  reg_user: 444444|workshop
  reg_pass: PASSWORD
  download_files:
    - https://access.cdn.redhat.com/content/origin/files/sha256/0e/0eaee0fdcf6884e201b34d265c2614b6872b4e02b3f7fe3a6de3e88b3043c6ec/ansible-automation-platform-setup-2.4-13.tar.gz?user=edc669ff3xxxxxx
    - https://access.cdn.redhat.com/content/origin/files/sha256/00/0009626a82069791ce785f1b5883dda241ba849eeef378bca3d21562c16db894/ansible-automation-platform-setup-2.6-2.tar.gz?user=c8422xxxx
  ```
  - The variable "inv24_dest" should be changed according to the version of the update/files
  - The variables "reg_user" and "reg_pass" should be generated from [Registry Service Accounts](https://access.redhat.com/terms-based-registry/)
  - The variable "download_files" should be a list of two links generated from [Downloads > Red Hat Ansible Automation Platform Product Software](https://access.redhat.com/downloads/content/480), which should contain AAP's setup scripts. One should be for AAP 2.4 RHEL9, and the other one AAP 2.5 or 2.6 RHEL9.
  - <img width="1568" height="998" alt="Screenshot 2025-11-11 at 3 12 02 PM" src="https://github.com/user-attachments/assets/23f13e60-b378-49f0-a825-46305f9cd6e4" />

    
