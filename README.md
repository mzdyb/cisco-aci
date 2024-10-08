# Automating Cisco ACI with Ansible
This projects shows how can we automate Cisco ACI using Ansible Automation Platform. Two use cases are presented here:
1. Configuring Cisco ACI objects
2. Querying ACI Controller to collect ACI configuration

## Configuring Cisco ACI objects
Cisco ACI provides centralized approach to manage physical and virtual networks and one of its goals is to simplify and unify network management. But increased operational efficiency might not be fully experienced if we use manual operations via GUI to configure ACI. This so called 'ClickOps' requires many steps in GUI and is difficult to document (often requires screenshots of GUI configuration). Ansible provides powerful and robust approach to get rid of ClickOps and fully switch to Automation. In this example the following ACI Objects are configured with Ansible:
![image](https://github.com/mzdyb/cisco-aci/assets/49950423/c5446d6b-5c04-48a6-b44b-25317cc7ed70)


**Essential steps to automate with Ansible:**
1. Create inventory  
   In inventory we are defining the list of target hosts we want to automate. In our case it is ACI Controller
   ```
   [apic]
   apic1 ansible_host=sandboxapicdc.cisco.com
   ```
   We are also defining variables related to connectivity to target host
   ```
   [apic:vars]
   ansible_connection=ansible.netcommon.httpapi
   ansible_network_os=cisco.aci.aci
   ansible_user=admin
   ansible_password='changeme'
   ansible_httpapi_validate_certs=false
   ansible_httpapi_use_ssl=true
   ```
   As we can see _httpapi plugin_ is used here to connect to ACI Controller. This is the recommended way to connect to APIC and offers a number of benefits like ability to defining credentials only once in inventory as opposed to including them in each task, authenticating per playbook run instead of per every tasks run etc. A good explanation of how _httpi plugin_ works and its benefits is provided here: [Cisco ACI httpapi plugin](https://www.ciscolive.com/on-demand/on-demand-library.html?search=httpapi#/session/1707505590105001pxJm)
     

3. Create data structure with variables reflecting ACI configuration  
   This is crucial step and in more advanced scenarios it might serve as Source of Truth (SoT) for our Cisco ACI configuration. SoT is central repository for configurations with up to date and reliable information. It serves as the reference point for desired state of the infrastructure. SoT documents our configuration so we don't need screenshots anymore and we are enabling full automation using Ansible. If we move SoT to Version Control System like GitHub we are also able to implement NetDevOps approach which brings benefits from Software Developement realm to network operations. The example of such approach is presented in the following GitHub project: [NetDevOps with Ansible Automation Platform](https://github.com/mzdyb/netdevops)

4. Write Ansible playbooks and use powerful Ansible Automation Platform features like Automation Workflows, RBAC etc.

   **Playbooks**  
   In this project Ansible Roles are used which allows to write modular/reusable automation code. Thanks to using Roles the whole playbook to implement our ACI Objects looks as simple as this:
   ```
   ---
   - name: Configure ACI
     hosts: apic

     roles:
       - configure_application
   ```
   **Automation Workflows**  
   Workflows allow creating visual logical representation of automation jobs sequence based on the result of the previous jobs run in the sequence. In this project modularity in Workflows is implemented by using Ansible Tags in Ansible Roles. The example of Automation Workflow from Ansible Automation Platform implementing automation defined in _configure_aci.yml_ playbook is shown below:

   ![image](https://github.com/mzdyb/cisco-aci/assets/49950423/22b57cfd-a4b0-447d-a153-27e3166cf091)
   The above approach is called _**Push of the button automation**_ as it allows to create sophisticated automation logic and to run it by just clicking "Run" button in the Workflow.

## Querying ACI Controller to collect ACI configuration
The second use case presented in this project is to collect ACI configuration from APIC. Ansible modules for ACI configuration have three possible states: 
- _present_ and _absent_ for adding and removing ACI Objects configuration
- _query_ for collecting Objects' configuration from Controller

In this example _query_ state is used to collect configuration of Tenant Networking objects: Tenants, VRFs, Bridge Domains and Subnets. This kind of query provides all configuration data related to queried objects and it might be a lot of data depending on queried objects so collected data is filtered to include only configuration parameters used by Ansible in the first use case in this project. For this purpose _json_query_ filter is used:
```
     {{ tenant_data.current |
         json_query("[?contains(fvTenant.attributes.dn,'production')].{
            name: fvTenant.attributes.name,
            description: fvTenant.attributes.descr
            }"
         )
      }}
```
As we can see only Tenant _name_ and _description_ are collected from APIC and only for Tenant named 'Production' which reflects configuration variables defined in _host_vars/apic1_ file.

## Remarks
For production environment passwords should not be stored in clear text in the inventory. Instead environment variables to export credentials should be used or ansible vault for password encryption. In case of Ansible Automation Platform the best approach is to use Custom Credentials with environment variables like in the example below.  

_Custom Credential Input configuration:_
```
fields:
  - id: username
    type: string
    label: APIC Username
    secret: false
  - id: password
    type: string
    label: APIC Password
    secret: true
```
_Custom Credential Injector configuration:_
```
env:
  ACI_PASSWORD: '{{ password }}'
  ACI_USERNAME: '{{ username }}'
```

If you are interested in ACI configuration approach with Source of True moved to Version Control System like GitHub/GitLab please check the ACI as a Code project from my Red Hat colleague Tony Dubiel: [ACI-as-Code](https://gitlab.com/redhatautomation/network_demos/-/blob/main/cisco_aci/)

## Feedback
Feedback is always welcome! If you have any comments, please reach me out

## Author

[@mzdyb](https://www.linkedin.com/in/michal-zdyb-9aa4046/)
