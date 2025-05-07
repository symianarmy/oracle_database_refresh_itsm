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



+++++++++++++++++++

[ansible_admin@ctrl ansible]$ ansible-playbook ./playbooks/itsm_oracle_refresh_actual.yml 

PLAY [db_servers] **********************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.1.64 is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
ok: [192.168.1.64]
[WARNING]: Platform linux on host 192.168.1.108 is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
ok: [192.168.1.108]

TASK [debug] ***************************************************************************************************************************************************************************************************************************
ok: [192.168.1.64] => {
    "ansible_distribution": "OracleLinux"
}
ok: [192.168.1.108] => {
    "ansible_distribution": "OracleLinux"
}

TASK [Confirm Local Facts for Each RDS Oracle Instance being Refreshed from a PROD Schema | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] **********************************************
ok: [192.168.1.64] => {
    "ansible_local": {
        "export": {
            "localfacts": {
                "export_fact_ansible_env_path": "/home/ansible/admin",
                "export_fact_container_database_type": "legacy",
                "export_fact_export_flag": "on",
                "export_fact_oracle_database_environment_purpose": "PROD",
                "export_fact_oracle_database_environment_readonly": "true",
                "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR",
                "export_fact_oracle_export_user_name": "source",
                "export_fact_oracle_export_user_password": <REDACTED>,
                "export_fact_oracle_listener_port_number": "1521",
                "export_fact_oracle_local_hostname": "lnx072",
                "export_fact_oracle_rdbms_version": "19.0.0",
                "export_fact_oracle_sid_name": "SRCPROD",
                "export_fact_oracle_tns_admin": "/u01/app/oracle/product/19c/dbhome_1/network/admin",
                "export_fact_rdbms_home": "/u01/app/oracle/product/19c/dbhome_1",
                "export_fact_schema": "source",
                "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/",
                "import_fact_oracle_local_hostname": "",
                "import_fact_oracle_sid_name": "",
                "import_fact_schema": ""
            }
        }
    }
}
ok: [192.168.1.108] => {
    "ansible_local": {
        "export": {
            "localfacts": {
                "export_fact_ansible_env_path": "/home/ansible_admin",
                "export_fact_container_database_type": "legacy",
                "export_fact_export_flag": "on",
                "export_fact_oracle_database_environment_purpose": "UAT",
                "export_fact_oracle_database_environment_readonly": "false",
                "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR",
                "export_fact_oracle_export_user_name": "source",
                "export_fact_oracle_export_user_password": <REDACTED>,
                "export_fact_oracle_listener_port_number": "1521",
                "export_fact_oracle_local_hostname": "lnx073",
                "export_fact_oracle_rdbms_version": "19.0.0",
                "export_fact_oracle_sid_name": "SRCUAT",
                "export_fact_oracle_tns_admin": "/u01/app/oracle/product/19c/dbhome_1/network/admin",
                "export_fact_rdbms_home": "/u01/app/oracle/product/19c/dbhome_1",
                "export_fact_schema": "source",
                "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/",
                "import_fact_oracle_local_hostname": "lnx072",
                "import_fact_oracle_sid_name": "SRCPROD",
                "import_fact_schema": "source"
            }
        }
    }
}

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] *********************************************************************
ok: [192.168.1.64]
ok: [192.168.1.108]

TASK [Create Lockfile if Doesnt Exist | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] **************************************************************************************************
changed: [192.168.1.64]
changed: [192.168.1.108]

TASK [Generate Local SOURCE Db Export ParFile from a Centralized JINJA Refresh Template | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ************************************************
changed: [192.168.1.64]
changed: [192.168.1.108]

TASK [Export Source Schema to Scratchpad Usng the Custom Parfile | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ***********************************************************************
skipping: [192.168.1.108]
changed: [192.168.1.64]

TASK [Generate One or More Target Schema Refresh ParFiles from a Centralized JINJA Refresh Template | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ************************************
skipping: [192.168.1.64]
changed: [192.168.1.108]

TASK [Import into the Destination Db Usng the Custom Import ParFile | execute oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ********************************************************************
skipping: [192.168.1.64]
changed: [192.168.1.108]

PLAY RECAP *****************************************************************************************************************************************************************************************************************************
192.168.1.108              : ok=8    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
192.168.1.64               : ok=7    changed=3    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   

[ansible_admin@ctrl ansible]$ 






