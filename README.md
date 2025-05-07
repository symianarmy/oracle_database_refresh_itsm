itsm_oracle_refresh_wrapper.yml - this is the wrapper playbook that uses the Ansible Control Node to connect to a ServiceNow ticketing system, opens an Oracle database refresh task. The wrapper then calls an included second playbook:
itsm_oracle_refresh_actual.yml - this is the actual playbook that refreshes any or many lower environment databases (a UAT database in this demo) at the same time. Both playbooks have to work in tandem, entirely unattended. The DBAs only are notified when something breaks.

A run log:


[ansible_admin@ctrl ansible]$ ansible-playbook ./playbooks/itsm_oracle_refresh_wrapper.yml 

PLAY [RFRSH2025Q2 Omni-Client PROD_2_UAT | announce oracle_schema_refresh] *************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Auto-Create "RFRSH2025Q2 Omni-Client PROD_2_UAT" Task in ServiceNow | prepare oracle_schema_refresh] *****************************************************************************************************************************
[WARNING]: Encountered unknown value open while mapping field state.
[WARNING]: Encountered unknown value 3 while mapping field close_code.
[WARNING]: Encountered unknown value  while mapping field close_code.
changed: [localhost]

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Starting the Quarterly RFRSH2025Q2 Omni-Client PROD_2_UAT | prepare oracle_schema_refresh"
}

TASK [Change ServiceNow Refresh Task State to In-Progress | prepare oracle_schema_refresh] *********************************************************************************************************************************************
changed: [localhost]

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Verified & Opened an Oracle RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010116 in ServiceNow | execute oracle_schema_refresh"
}

TASK [Prepare Oracle Refresh Process | execute oracle_schema_refresh] ******************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Executing RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010116"
}

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Closing the RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010116"
}

TASK [Close ServiceNow Refresh Task as Completed | cleanup oracle_schema_refresh] ******************************************************************************************************************************************************
[WARNING]: Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code.
changed: [localhost]

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Has Now Completed the Quarterly RFRSH2025Q2 Omni-Client PROD_2_UAT Task by Executing INC0010116, Refresh ServiceNow SYS_ID \"6cbcc12a83512250a6565929feaad313\""
}

PLAY RECAP *****************************************************************************************************************************************************************************************************************************
localhost                  : ok=10   changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[ansible_admin@ctrl ansible]$ 





