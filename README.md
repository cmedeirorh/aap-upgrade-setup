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
* Also under **Infrastructure**, select **Inventories**
* Create (or use an existing one) an **Inventory**
  <img width="1339" height="683" alt="Screenshot 2025-11-11 at 1 12 16 PM" src="https://github.com/user-attachments/assets/feefef56-a262-4992-9532-60bd2e6613a7" />
  <img width="1885" height="931" alt="Screenshot 2025-11-11 at 1 16 19 PM" src="https://github.com/user-attachments/assets/ae0bb71a-e721-4ac5-8539-fc3afdf49a28" />
* Add a Source to create the inventory dynamically using the AWS plugin
  <img width="1820" height="768" alt="Screenshot 2025-11-11 at 1 20 03 PM" src="https://github.com/user-attachments/assets/c33eca0a-8cba-49aa-ac90-453b61b509d8" />
  <img width="1886" height="933" alt="Screenshot 2025-11-11 at 1 23 32 PM" src="https://github.com/user-attachments/assets/76fb5d20-19b5-43c6-ac17-032e6cace60a" />
  Use these variables for the plugin: [aws_inventory_source_variables](https://github.com/cmedeirorh/aap-upgrade-setup/blob/main/aws_inventory_source_variables.yml)





