Oracle Database Refresh ITSM Version 2.1.0.0 20250518.002
=========

This collection of playbooks refreshes one or more lower environment Oracle database schemas from a PROD database schema. It uses the ServiceNow ITSM +Ansible API functionality to request, seek approvals, schedule and auto-execute and/or rollback the refresh tasks unattended. 

Requirements
------------

This functionality relies on:
 1) Ansible custom facts, such as $ORACLE_HOME or $ORACLE_SID. The facts are either gathered at runtime locally on the hosts, or they are in the Ansible CTRL+ inventory file, or in the VAR file or specified at run time in the CLI. This information can also be supplied by parsing AWS / OCI tags.
 2) Ansible JINJA2 templates for each host. There are several templates. For the ServiceNow refresh task itself (priority, assignments, sheduling, apprvals), a parfile for Oracle export and import. Each template is analyzed by Ansible at run time for each refresh target , and a highly customized export or import parfile is created and used.
 3) Ansible playbooks that are called depending on the refresh type - for example refresh UAT from PROD only, or refresh DEV from UAT. The playbooks also use a basic decision making mechanism to rollback the refresh, if a specific set of errors is displayed by the import and all preliminary remediation protocls are exhausted.
 
Role Variables
--------------

In this iteration the variables are in the VAR directory. The ServiceNow ticketing system specified here is a test, trial instance. Replace with own URL. Same applies to the instance names and variables in the local ansible facts.

Dependencies
------------

Oracle RDBMS 19c and up
Ansible 2.9 and up
ServiceNow ITSM

Example Playbook
----------------

In this particular run we are refreshing a fleet of 3 lower environment databases (a CERT/PREPROD, DEV and UAT) from PROD. This could be a hundred of lower environemtns to be refreshed instead of the three. 

Here is the inventory fr this run:

# Ansible Inventory
[db_servers_prod]
PROD_ORACLE_ENVRNMNT ansible_ssh_host=192.168.1.64 my_host_alias=PROD_ORACLE_ENVRNMNT

[db_servers_uat]
UAT_ORACLE_ENVRNMNT ansible_ssh_host=192.168.1.108 my_host_alias=UAT_ORACLE_ENVRNMNT

[db_servers_cert]
PREPROD_ORACLE_ENVRNMNT ansible_ssh_host=192.168.1.127 my_host_alias=PREPROD_ORACLE_ENVRNMNT

[db_servers_dev]
#192.168.1.124
DEV_ORACLE_ENVRNMNT ansible_ssh_host=192.168.1.124 my_host_alias=DEV_ORACLE_ENVRNMNT



Here is the actual run:



[ansible_admin@ctrl ansible]$ ansible-playbook ./playbooks/itsm_oracle_refresh_wrapper.yml -kK 


SSH password: BECOME password[defaults to SSH password]: [WARNING]: Collection servicenow.itsm does not support Ansible version 2.14.17

PLAY [RFRSH2025Q2 Omni-Client PROD_2_UAT | announce oracle_schema_refresh] ****************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** ok: [localhost]

TASK [Auto-Create "RFRSH2025Q2 Omni-Client PROD_2_UAT" Task in ServiceNow | itsm_oracle_refresh_wrapper] ********************************************************************** [WARNING]: Encountered unknown value open while mapping field state. [WARNING]: Encountered unknown value 3 while mapping field close_code. [WARNING]: Encountered unknown value while mapping field close_code. changed: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Ansible Starting the Quarterly RFRSH2025Q2 Omni-Client PROD_2_UAT | itsm_oracle_refresh_wrapper" }

TASK [Change ServiceNow Refresh Task State to In-Progress | prepare oracle_schema_refresh] ************************************************************************************ changed: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Ansible Verified & Opened an Oracle RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010202 in ServiceNow | itsm_oracle_refresh_wrapper" }

TASK [Prepare Oracle Refresh Process | execute oracle_schema_refresh] ********************************************************************************************************* ok: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Ansible Executing RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010202 | itsm_oracle_refresh_wrapper" }

TASK [Cache the Refresh Task SYS_ID for All Playbooks to Re-Use | prepare oracle_database_refresh] **************************************************************************** ok: [localhost]

TASK [Write the Refresh Task SYS_ID Variable to the Disk on CTRL+ Node Alone | itsm_oracle_refresh_wrapper] ******************************************************************* changed: [localhost]

PLAY [db_servers_prod] ********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** [WARNING]: Platform linux on host PROD_ORCL_ENVRNMNT is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information. ok: [PROD_ORCL_ENVRNMNT]

TASK [debug] ****************************************************************************************************************************************************************** ok: [PROD_ORCL_ENVRNMNT] => { "ansible_distribution": "OracleLinux" }

TASK [Version 2.7. Confirm Local Facts for Each Oracle Instance being Refreshed "source" schema in the "SRCPROD" Database on "lnx072" | itsm_oracle_refresh_actual_prod] ****** ok: [PROD_ORCL_ENVRNMNT] => { "ansible_local": { "export": { "localfacts": { "export_fact_ansible_env_path": "/home/ansible/admin", "export_fact_container_database_type": "legacy", "export_fact_export_flag": "on", "export_fact_oracle_database_environment_purpose": "PROD", "export_fact_oracle_database_environment_readonly": "true", "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR", "export_fact_oracle_export_user_name": "source", "export_fact_oracle_export_user_password": , "export_fact_oracle_listener_port_number": "1521", "export_fact_oracle_local_hostname": "lnx072", "export_fact_oracle_rdbms_version": "19.0.0", "export_fact_oracle_sid_name": "SRCPROD", "export_fact_oracle_tns_admin": "/u01/app/oracle/product/19c/dbhome_1/network/admin", "export_fact_rdbms_home": "/u01/app/oracle/product/19c/dbhome_1", "export_fact_schema": "source", "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/", "import_fact_oracle_local_hostname": "", "import_fact_oracle_sid_name": "", "import_fact_schema": "" } } } }

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running for "source" schema in the "SRCPROD" Database on "lnx072" | itsm_oracle_refresh_actual_prod] *************** ok: [PROD_ORCL_ENVRNMNT]

TASK [Create Lockfile if Doesnt Exist for "source" schema in the "SRCPROD" Database on "lnx072" | itsm_oracle_refresh_actual_prod] ******************************************** changed: [PROD_ORCL_ENVRNMNT]

TASK [Generate Local SOURCE Db Export ParFile from a Centralized JINJA Refresh Template | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072" | itsm_oracle_refresh_actual_prod] *** changed: [PROD_ORCL_ENVRNMNT]

TASK [Export Source Schema to Scratchpad Usng the Custom Parfile for "source" schema in the "SRCPROD" Database on "lnx072" | itsm_oracle_refresh_actual_prod] ***************** changed: [PROD_ORCL_ENVRNMNT]

PLAY [db_servers_dev] *********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** [WARNING]: Platform linux on host DEV_ORCL_ENVRNMNT is using the discovered Python interpreter at /usr/bin/python3.11, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information. ok: [DEV_ORCL_ENVRNMNT]

TASK [debug] ****************************************************************************************************************************************************************** ok: [DEV_ORCL_ENVRNMNT] => { "ansible_distribution": "OracleLinux" }

TASK [Version 2.7. Confirm Local Facts for Each Oracle Instance being Refreshed "source" schema in the "SRCDEV" Database on "lnx075" | itsm_oracle_refresh_actual_uat] ******** ok: [DEV_ORCL_ENVRNMNT] => { "ansible_local": { "export": { "localfacts": { "export_fact_ansible_env_path": "/home/ansible/admin", "export_fact_container_database_type": "legacy", "export_fact_export_flag": "on", "export_fact_oracle_database_environment_purpose": "DEV", "export_fact_oracle_database_environment_readonly": "false", "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR_LOCAL", "export_fact_oracle_export_user_name": "source", "export_fact_oracle_export_user_password": , "export_fact_oracle_listener_port_number": "1521", "export_fact_oracle_local_hostname": "lnx075", "export_fact_oracle_rdbms_version": "21.0.0", "export_fact_oracle_sid_name": "SRCDEV", "export_fact_oracle_tns_admin": "/u01/app/oracle/product/21.0.0/dbhome_1/soft/network/admin", "export_fact_rdbms_home": "/u01/app/oracle/product/21.0.0/dbhome_1/soft", "export_fact_schema": "source", "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/", "import_fact_oracle_local_hostname": "lnx072", "import_fact_oracle_sid_name": "SRCPROD", "import_fact_schema": "source" } } } }

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running for "source" schema in the "SRCDEV" Database on "lnx075" | itsm_oracle_refresh_actual_uat] ***************** ok: [DEV_ORCL_ENVRNMNT]

TASK [Create Lockfile if Doesnt Exist | prepare oracle_database_refresh of the "source" schema in the "SRCDEV" Database on "lnx075" | itsm_oracle_refresh_actual_uat] ********* changed: [DEV_ORCL_ENVRNMNT]

TASK [Generate One or More Target Schema Import ParFiles from a Centralized JINJA Refresh Template | itsm_oracle_refresh_actual_uat] ****************************************** changed: [DEV_ORCL_ENVRNMNT]

TASK [Import into the Destination Db Usng the Custom Import ParFile | execute oracle_database_refresh of the "source" schema in the "SRCDEV" Database on "lnx075" | itsm_oracle_refresh_actual_uat] *** changed: [DEV_ORCL_ENVRNMNT]

PLAY [db_servers_uat] *********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** [WARNING]: Platform linux on host UAT_ORCL_ENVRNMNT is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information. ok: [UAT_ORCL_ENVRNMNT]

TASK [debug] ****************************************************************************************************************************************************************** ok: [UAT_ORCL_ENVRNMNT] => { "ansible_distribution": "OracleLinux" }

TASK [Version 2.7. Confirm Local Facts for Each Oracle Instance being Refreshed "source" schema in the "SRCUAT" Database on "lnx073" | itsm_oracle_refresh_actual_uat] ******** ok: [UAT_ORCL_ENVRNMNT] => { "ansible_local": { "export": { "localfacts": { "export_fact_ansible_env_path": "/home/ansible_admin", "export_fact_container_database_type": "legacy", "export_fact_export_flag": "on", "export_fact_oracle_database_environment_purpose": "UAT", "export_fact_oracle_database_environment_readonly": "false", "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR", "export_fact_oracle_export_user_name": "source", "export_fact_oracle_export_user_password": , "export_fact_oracle_listener_port_number": "1521", "export_fact_oracle_local_hostname": "lnx073", "export_fact_oracle_rdbms_version": "19.0.0", "export_fact_oracle_sid_name": "SRCUAT", "export_fact_oracle_tns_admin": "/u01/app/oracle/product/19c/dbhome_1/network/admin", "export_fact_rdbms_home": "/u01/app/oracle/product/19c/dbhome_1", "export_fact_schema": "source", "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/", "import_fact_oracle_local_hostname": "lnx072", "import_fact_oracle_sid_name": "SRCPROD", "import_fact_schema": "source" } } } }

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running for "source" schema in the "SRCUAT" Database on "lnx073" | itsm_oracle_refresh_actual_uat] ***************** ok: [UAT_ORCL_ENVRNMNT]

TASK [Create Lockfile if Doesnt Exist | prepare oracle_database_refresh of the "source" schema in the "SRCUAT" Database on "lnx073" | itsm_oracle_refresh_actual_uat] ********* changed: [UAT_ORCL_ENVRNMNT]

TASK [Generate One or More Target Schema Import ParFiles from a Centralized JINJA Refresh Template | itsm_oracle_refresh_actual_uat] ****************************************** changed: [UAT_ORCL_ENVRNMNT]

TASK [Import into the Destination Db Usng the Custom Import ParFile | execute oracle_database_refresh of the "source" schema in the "SRCUAT" Database on "lnx073" | itsm_oracle_refresh_actual_uat] *** changed: [UAT_ORCL_ENVRNMNT]

PLAY [db_servers_cert] ********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** [WARNING]: Platform linux on host PREPROD_ORCL_ENVRNMNT is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information. ok: [PREPROD_ORCL_ENVRNMNT]

TASK [debug] ****************************************************************************************************************************************************************** ok: [PREPROD_ORCL_ENVRNMNT] => { "ansible_distribution": "OracleLinux" }

TASK [Version 2.7. Confirm Local Facts for Each Oracle Instance being Refreshed | itsm_oracle_refrsh_actual] ****************************************************************** ok: [PREPROD_ORCL_ENVRNMNT] => { "ansible_local": { "export": { "localfacts": { "export_fact_ansible_env_path": "/home/ansible/admin", "export_fact_container_database_type": "legacy", "export_fact_export_flag": "on", "export_fact_oracle_database_environment_purpose": "CERT", "export_fact_oracle_database_environment_readonly": "false", "export_fact_oracle_export_datapump_dir": "DATA_PUMP_DIR", "export_fact_oracle_export_user_name": "source", "export_fact_oracle_export_user_password": , "export_fact_oracle_listener_port_number": "1521", "export_fact_oracle_local_hostname": "lnx050", "export_fact_oracle_rdbms_version": "19.0.0", "export_fact_oracle_sid_name": "SRCCERT", "export_fact_oracle_tns_admin": "/u01/app/oracle/product/19c/dbhome_1/network/admin", "export_fact_rdbms_home": "/u01/app/oracle/product/19c/dbhome_1", "export_fact_schema": "source", "export_fact_shared_scratchpad_s3_bucket": "/mnt/hgfs/Linux_share/scratchpad/", "import_fact_oracle_local_hostname": "lnx072", "import_fact_oracle_sid_name": "SRCPROD", "import_fact_schema": "source" } } } }

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running for "source" schema in the "SRCCERT" Database on "lnx050" | prepare oracle_database_refresh of the | itsm_oracle_refresh_actual_cert] *** ok: [PREPROD_ORCL_ENVRNMNT]

TASK [Create Lockfile if Doesnt Exist | prepare oracle_database_refresh of the "source" schema in the "SRCCERT" Database on "lnx050" | itsm_oracle_refresh_actual_cert] ******* changed: [PREPROD_ORCL_ENVRNMNT]

TASK [Generate One or More Target Schema Import ParFiles from a Centralized JINJA Refresh Template | itsm_oracle_refresh_actual_cert] ***************************************** changed: [PREPROD_ORCL_ENVRNMNT]

TASK [Import into the Destination Db Usng the Custom Import ParFile "source" schema in the "SRCCERT" Database on "lnx050" | itsm_oracle_refresh_actual_cert] ****************** changed: [PREPROD_ORCL_ENVRNMNT]

PLAY [RFRSH2025Q2 Omni-Client PROD_2_UAT | announce oracle_schema_refresh] ****************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************** ok: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Version 3.2. Ansible is Closing the RFRSH2025Q2 Omni-Client PROD_2_UAT Task as Successful" }

TASK [Close ServiceNow Refresh Task as Completed | cleanup oracle_schema_refresh] ********************************************************************************************* [WARNING]: Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code. changed: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Ansible is Entering the Following in the Closing Notes (RFRSH2025Q2 Omni-Client PROD_2_UAT Task)" }

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "closing_notes_out": { "changed": true, "diff": { "after": { "active": "true", "activity_due": "", "additional_assignee_list": "", "approval": "not requested", "approval_history": "", "assigned_to": "", "assignment_group": "", "attachments": [], "business_duration": "", "business_impact": "", "business_stc": "", "calendar_duration": "", "calendar_stc": "", "caller_id": "6816f79cc0a8016401c5a33be04be441", "category": "inquiry", "cause": "", "caused_by": "", "child_incidents": "0", "close_code": "", "close_notes": "Closed", "closed_at": "", "closed_by": "", "comments": "", "contact_type": "", "contract": "", "correlation_display": "", "correlation_id": "", "delivery_plan": "", "delivery_task": "", "description": "", "due_date": "", "escalation": "0", "expected_start": "", "follow_up": "", "group_list": "", "hold_reason": "", "impact": "low", "incident_state": "1", "knowledge": "false", "location": "", "made_sla": "true", "notify": "1", "number": "INC0010202", "opened_at": "2025-05-13 01:22:05", "opened_by": "775acf7e83812210a6565929feaad3f0", "order": "", "origin_id": "", "origin_table": "", "parent": "", "parent_incident": "", "priority": "5", "problem_id": "9", "reassignment_count": "0", "reopen_count": "0", "reopened_by": "", "reopened_time": "", "resolved_at": "", "resolved_by": "", "rfc": "", "route_reason": "", "severity": "3", "short_description": "Refresh All UAT Databases from PROD", "sla_due": "", "state": "new", "subcategory": "", "sys_class_name": "incident", "sys_created_by": "aes.creator", "sys_created_on": "2025-05-13 01:22:05", "sys_domain": "global", "sys_domain_path": "/", "sys_id": "7e17d0a483a96a50a6565929feaad35b", "sys_mod_count": "1", "sys_tags": "", "sys_updated_by": "aes.creator", "sys_updated_on": "2025-05-13 01:23:42", "task_effective_number": "INC0010202", "time_worked": "", "universal_request": "", "upon_approval": "proceed", "upon_reject": "cancel", "urgency": "low", "user_input": "", "watch_list": "", "work_end": "", "work_start": "" }, "before": { "active": "true", "activity_due": "", "additional_assignee_list": "", "approval": "not requested", "approval_history": "", "assigned_to": "", "assignment_group": "", "attachments": [], "business_duration": "", "business_impact": "", "business_stc": "", "calendar_duration": "", "calendar_stc": "", "caller_id": "6816f79cc0a8016401c5a33be04be441", "category": "inquiry", "cause": "", "caused_by": "", "child_incidents": "0", "close_code": "", "close_notes": "", "closed_at": "", "closed_by": "", "comments": "", "contact_type": "", "contract": "", "correlation_display": "", "correlation_id": "", "delivery_plan": "", "delivery_task": "", "description": "", "due_date": "", "escalation": "0", "expected_start": "", "follow_up": "", "group_list": "", "hold_reason": "", "impact": "low", "incident_state": "1", "knowledge": "false", "location": "", "made_sla": "true", "notify": "1", "number": "INC0010202", "opened_at": "2025-05-13 01:22:05", "opened_by": "775acf7e83812210a6565929feaad3f0", "order": "", "origin_id": "", "origin_table": "", "parent": "", "parent_incident": "", "priority": "5", "problem_id": "9", "reassignment_count": "0", "reopen_count": "0", "reopened_by": "", "reopened_time": "", "resolved_at": "", "resolved_by": "", "rfc": "", "route_reason": "", "severity": "3", "short_description": "Refresh All UAT Databases from PROD", "sla_due": "", "state": "new", "subcategory": "", "sys_class_name": "incident", "sys_created_by": "aes.creator", "sys_created_on": "2025-05-13 01:22:05", "sys_domain": "global", "sys_domain_path": "/", "sys_id": "7e17d0a483a96a50a6565929feaad35b", "sys_mod_count": "0", "sys_tags": "", "sys_updated_by": "aes.creator", "sys_updated_on": "2025-05-13 01:22:05", "task_effective_number": "INC0010202", "time_worked": "", "universal_request": "", "upon_approval": "proceed", "upon_reject": "cancel", "urgency": "low", "user_input": "", "watch_list": "", "work_end": "", "work_start": "" } }, "failed": false, "record": { "active": "true", "activity_due": "", "additional_assignee_list": "", "approval": "not requested", "approval_history": "", "assigned_to": "", "assignment_group": "", "attachments": [], "business_duration": "", "business_impact": "", "business_stc": "", "calendar_duration": "", "calendar_stc": "", "caller_id": "6816f79cc0a8016401c5a33be04be441", "category": "inquiry", "cause": "", "caused_by": "", "child_incidents": "0", "close_code": "", "close_notes": "Closed", "closed_at": "", "closed_by": "", "comments": "", "contact_type": "", "contract": "", "correlation_display": "", "correlation_id": "", "delivery_plan": "", "delivery_task": "", "description": "", "due_date": "", "escalation": "0", "expected_start": "", "follow_up": "", "group_list": "", "hold_reason": "", "impact": "low", "incident_state": "1", "knowledge": "false", "location": "", "made_sla": "true", "notify": "1", "number": "INC0010202", "opened_at": "2025-05-13 01:22:05", "opened_by": "775acf7e83812210a6565929feaad3f0", "order": "", "origin_id": "", "origin_table": "", "parent": "", "parent_incident": "", "priority": "5", "problem_id": "9", "reassignment_count": "0", "reopen_count": "0", "reopened_by": "", "reopened_time": "", "resolved_at": "", "resolved_by": "", "rfc": "", "route_reason": "", "severity": "3", "short_description": "Refresh All UAT Databases from PROD", "sla_due": "", "state": "new", "subcategory": "", "sys_class_name": "incident", "sys_created_by": "aes.creator", "sys_created_on": "2025-05-13 01:22:05", "sys_domain": "global", "sys_domain_path": "/", "sys_id": "7e17d0a483a96a50a6565929feaad35b", "sys_mod_count": "1", "sys_tags": "", "sys_updated_by": "aes.creator", "sys_updated_on": "2025-05-13 01:23:42", "task_effective_number": "INC0010202", "time_worked": "", "universal_request": "", "upon_approval": "proceed", "upon_reject": "cancel", "urgency": "low", "user_input": "", "watch_list": "", "work_end": "", "work_start": "" }, "warnings": [ "Encountered unknown value while mapping field close_code.", "Encountered unknown value while mapping field close_code.", "Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code.", "Encountered unknown value while mapping field close_code." ] } }

TASK [Cleanup the Temp Refresh Files | cleanup oracle_schema_refresh] ********************************************************************************************************* changed: [localhost]

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Refresh Task SYS_ID Purged from Schedule and Ansible CTRL+ Disk" }

TASK [debug] ****************************************************************************************************************************************************************** ok: [localhost] => { "msg": "Ansible Has Now Closed the RFRSH2025Q2 Omni-Client PROD_2_UAT Task as Successful. Thank You and Have a Nice Day." }

PLAY RECAP ******************************************************************************************************************************************************************** DEV_ORCL_ENVRNMNT : ok=7 changed=3 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
PREPROD_ORCL_ENVRNMNT : ok=7 changed=3 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
PROD_ORCL_ENVRNMNT : ok=7 changed=3 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
UAT_ORCL_ENVRNMNT : ok=7 changed=3 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
localhost : ok=17 changed=5 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0

[ansible_admin@ctrl ansible]$


License
-------

BSD

Author Information
------------------

Symian Army
