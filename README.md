# Automating Cisco ACI with Ansible
This projects shows how can we automate Cisco ACI using Ansible Automation Platform. Two use cases are presented here:
1. Configuring Cisco ACI objects
2. Querying ACI Controller to collect ACI configuration

## Configuring Cisco ACI objects
Cisco ACI provides centralized approach to manage physical and virtual networks and one of its goals is to simplify and unify network management. But increased operational efficiency might not be fully experienced if we use manual operations via GUI to configure ACI. This so called 'ClickOps' requires many steps in GUI and is difficult to document (ofter requires screenshots of GUI configuration). Ansible provides powerful and robust approach to get rid of ClickOps and switch to Automation. In this example the following ACI Objects are configured with Ansible:
![image](https://github.com/mzdyb/cisco-aci/assets/49950423/466cceba-7180-4fdc-9880-237928534732)  

Essential steps to automate with Ansible:
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
   As we can see _httpapi plugin_ is used here to connect to ACI Controller. This is the recommended way to connect to APIC and offers a number of benefits like ability defining credentials only once in inventory as opposed to including them in each task, authenticating per playbook run instead of per each tasks and so on. A good explanation of how httpi plugin works is provided here:  
   [Cisco ACI httpapi plugin](https://www.ciscolive.com/on-demand/on-demand-library.html?search=httpapi#/session/1707505590105001pxJm)  
