Files, purpose and sequence:

1. The "wrapper" playbook creates a refresh task and waits it till it auto-approved by the ServiceNow ITSM.
2. The "actual" playbook does the actual refreshes of several lower environment databases from PROD.
3. The "finish" playbook closes the refresh task, using the facts from the "wrapper" playbook.

The example below refreshes:
- a UAT Oracle database SRCUAT on [192.168.1.108] lnx072
- from its PROD database SRCPROD on [192.168.1.64] lnx073
- from an Ansible CTRL+ node, mentioned only as a "localhost" from which the main "wrapper" playbook is launched.
- the close-loop ServiceNow ITSM server overseeing the approvals and scheduling is a trial instance: https://dev336605.service-now.com

We could add an additional array of lower environment databases added to this particular UAT refresh, lets say DEV, STG, CERT, MO, VAR, PERF, PRPROD - all defined within the same hosts file or by using a dedcated ServiceNow or Ansible JINJA2 template. That functionality is coming.

The functionality relies on :
1. Local custom facts, this can be replaced with AWS tags.
2. Ansible JINJA2 templates for dynamic creation of import/export parfiles.


To run this from the Ansible CTRL+ node:


[ansible_admin@ctrl ansible]$ ansible-playbook ./playbooks/itsm_oracle_refresh_wrapper.yml 

PLAY [RFRSH2025Q2 Omni-Client PROD_2_UAT | announce oracle_schema_refresh] ***************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Auto-Create "RFRSH2025Q2 Omni-Client PROD_2_UAT" Task in ServiceNow | prepare oracle_schema_refresh] *******************************************************************************************************
[WARNING]: Encountered unknown value open while mapping field state.
[WARNING]: Encountered unknown value 3 while mapping field close_code.
[WARNING]: Encountered unknown value  while mapping field close_code.
changed: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Starting the Quarterly RFRSH2025Q2 Omni-Client PROD_2_UAT | prepare oracle_schema_refresh"
}

TASK [Change ServiceNow Refresh Task State to In-Progress | prepare oracle_schema_refresh] ***********************************************************************************************************************
changed: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Verified & Opened an Oracle RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010125 in ServiceNow | execute oracle_schema_refresh"
}

TASK [Prepare Oracle Refresh Process | execute oracle_schema_refresh] ********************************************************************************************************************************************
ok: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Executing RFRSH2025Q2 Omni-Client PROD_2_UAT Task INC0010125"
}

TASK [Cache the Refresh Task SYS_ID for All Playbooks to Re-Use | prepare oracle_database_refresh] ***************************************************************************************************************
ok: [localhost]

TASK [Write the Refresh Task SYS_ID Variable to the Disk on CTRL+ Node Alone | prepare oracle_database_refresh] **************************************************************************************************
changed: [localhost]

PLAY [db_servers] ************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.1.64 is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
ok: [192.168.1.64]
[WARNING]: Platform linux on host 192.168.1.108 is using the discovered Python interpreter at /usr/libexec/platform-python, but future installation of another Python interpreter could change the meaning of
that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
ok: [192.168.1.108]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [192.168.1.64] => {
    "ansible_distribution": "OracleLinux"
}
ok: [192.168.1.108] => {
    "ansible_distribution": "OracleLinux"
}

TASK [Confirm Local Facts for Each Oracle Instance being Refreshed | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ***********************************************
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
                "export_fact_oracle_export_user_password": "oracle",
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
                "export_fact_oracle_export_user_password": "oracle",
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

TASK [Check if Lockfile Exists or an Earlier Refresh Still Running | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ***********************************************
ok: [192.168.1.108]
ok: [192.168.1.64]

TASK [Create Lockfile if Doesnt Exist | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] ****************************************************************************
changed: [192.168.1.64]
changed: [192.168.1.108]

TASK [Generate Local SOURCE Db Export ParFile from a Centralized JINJA Refresh Template | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] **************************
changed: [192.168.1.108]
changed: [192.168.1.64]

TASK [Export Source Schema to Scratchpad Usng the Custom Parfile | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] *************************************************
skipping: [192.168.1.108]
changed: [192.168.1.64]

TASK [Generate One or More Target Schema Refresh ParFiles from a Centralized JINJA Refresh Template | prepare oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] **************
skipping: [192.168.1.64]
changed: [192.168.1.108]

TASK [Import into the Destination Db Usng the Custom Import ParFile | execute oracle_database_refresh of the "source" schema in the "SRCPROD" Database on "lnx072"] **********************************************
skipping: [192.168.1.64]
changed: [192.168.1.108]

PLAY RECAP *******************************************************************************************************************************************************************************************************
192.168.1.108              : ok=8    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
192.168.1.64               : ok=7    changed=3    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
localhost                  : ok=9    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[ansible_admin@ctrl ansible]$ ansible-playbook ./playbooks/itsm_oracle_refresh_finish.yml

PLAY [RFRSH2025Q2 Omni-Client PROD_2_UAT | announce oracle_schema_refresh] ***************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Version 3.2. Ansible is Closing the RFRSH2025Q2 Omni-Client PROD_2_UAT Task as Successful"
}

TASK [Close ServiceNow Refresh Task as Completed | cleanup oracle_schema_refresh] ********************************************************************************************************************************
[WARNING]: Encountered unknown value  while mapping field close_code.
[WARNING]: Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code.
changed: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible is Entering the Following in the Closing Notes (RFRSH2025Q2 Omni-Client PROD_2_UAT Task)"
}

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "closing_notes_out": {
        "changed": true,
        "diff": {
            "after": {
                "active": "true",
                "activity_due": "",
                "additional_assignee_list": "",
                "approval": "not requested",
                "approval_history": "",
                "assigned_to": "",
                "assignment_group": "",
                "attachments": [],
                "business_duration": "",
                "business_impact": "",
                "business_stc": "",
                "calendar_duration": "",
                "calendar_stc": "",
                "caller_id": "6816f79cc0a8016401c5a33be04be441",
                "category": "inquiry",
                "cause": "",
                "caused_by": "",
                "child_incidents": "0",
                "close_code": "",
                "close_notes": "Closed",
                "closed_at": "",
                "closed_by": "",
                "comments": "",
                "contact_type": "",
                "contract": "",
                "correlation_display": "",
                "correlation_id": "",
                "delivery_plan": "",
                "delivery_task": "",
                "description": "",
                "due_date": "",
                "escalation": "0",
                "expected_start": "",
                "follow_up": "",
                "group_list": "",
                "hold_reason": "",
                "impact": "low",
                "incident_state": "1",
                "knowledge": "false",
                "location": "",
                "made_sla": "true",
                "notify": "1",
                "number": "INC0010125",
                "opened_at": "2025-05-07 11:37:15",
                "opened_by": "775acf7e83812210a6565929feaad3f0",
                "order": "",
                "origin_id": "",
                "origin_table": "",
                "parent": "",
                "parent_incident": "",
                "priority": "5",
                "problem_id": "9",
                "reassignment_count": "0",
                "reopen_count": "0",
                "reopened_by": "",
                "reopened_time": "",
                "resolved_at": "",
                "resolved_by": "",
                "rfc": "",
                "route_reason": "",
                "severity": "3",
                "short_description": "Refresh All UAT Databases from PROD",
                "sla_due": "",
                "state": "new",
                "subcategory": "",
                "sys_class_name": "incident",
                "sys_created_by": "aes.creator",
                "sys_created_on": "2025-05-07 11:37:15",
                "sys_domain": "global",
                "sys_domain_path": "/",
                "sys_id": "3b5aab66835d2250a6565929feaad36b",
                "sys_mod_count": "1",
                "sys_tags": "",
                "sys_updated_by": "aes.creator",
                "sys_updated_on": "2025-05-07 11:44:15",
                "task_effective_number": "INC0010125",
                "time_worked": "",
                "universal_request": "",
                "upon_approval": "proceed",
                "upon_reject": "cancel",
                "urgency": "low",
                "user_input": "",
                "watch_list": "",
                "work_end": "",
                "work_start": ""
            },
            "before": {
                "active": "true",
                "activity_due": "",
                "additional_assignee_list": "",
                "approval": "not requested",
                "approval_history": "",
                "assigned_to": "",
                "assignment_group": "",
                "attachments": [],
                "business_duration": "",
                "business_impact": "",
                "business_stc": "",
                "calendar_duration": "",
                "calendar_stc": "",
                "caller_id": "6816f79cc0a8016401c5a33be04be441",
                "category": "inquiry",
                "cause": "",
                "caused_by": "",
                "child_incidents": "0",
                "close_code": "",
                "close_notes": "",
                "closed_at": "",
                "closed_by": "",
                "comments": "",
                "contact_type": "",
                "contract": "",
                "correlation_display": "",
                "correlation_id": "",
                "delivery_plan": "",
                "delivery_task": "",
                "description": "",
                "due_date": "",
                "escalation": "0",
                "expected_start": "",
                "follow_up": "",
                "group_list": "",
                "hold_reason": "",
                "impact": "low",
                "incident_state": "1",
                "knowledge": "false",
                "location": "",
                "made_sla": "true",
                "notify": "1",
                "number": "INC0010125",
                "opened_at": "2025-05-07 11:37:15",
                "opened_by": "775acf7e83812210a6565929feaad3f0",
                "order": "",
                "origin_id": "",
                "origin_table": "",
                "parent": "",
                "parent_incident": "",
                "priority": "5",
                "problem_id": "9",
                "reassignment_count": "0",
                "reopen_count": "0",
                "reopened_by": "",
                "reopened_time": "",
                "resolved_at": "",
                "resolved_by": "",
                "rfc": "",
                "route_reason": "",
                "severity": "3",
                "short_description": "Refresh All UAT Databases from PROD",
                "sla_due": "",
                "state": "new",
                "subcategory": "",
                "sys_class_name": "incident",
                "sys_created_by": "aes.creator",
                "sys_created_on": "2025-05-07 11:37:15",
                "sys_domain": "global",
                "sys_domain_path": "/",
                "sys_id": "3b5aab66835d2250a6565929feaad36b",
                "sys_mod_count": "0",
                "sys_tags": "",
                "sys_updated_by": "aes.creator",
                "sys_updated_on": "2025-05-07 11:37:15",
                "task_effective_number": "INC0010125",
                "time_worked": "",
                "universal_request": "",
                "upon_approval": "proceed",
                "upon_reject": "cancel",
                "urgency": "low",
                "user_input": "",
                "watch_list": "",
                "work_end": "",
                "work_start": ""
            }
        },
        "failed": false,
        "record": {
            "active": "true",
            "activity_due": "",
            "additional_assignee_list": "",
            "approval": "not requested",
            "approval_history": "",
            "assigned_to": "",
            "assignment_group": "",
            "attachments": [],
            "business_duration": "",
            "business_impact": "",
            "business_stc": "",
            "calendar_duration": "",
            "calendar_stc": "",
            "caller_id": "6816f79cc0a8016401c5a33be04be441",
            "category": "inquiry",
            "cause": "",
            "caused_by": "",
            "child_incidents": "0",
            "close_code": "",
            "close_notes": "Closed",
            "closed_at": "",
            "closed_by": "",
            "comments": "",
            "contact_type": "",
            "contract": "",
            "correlation_display": "",
            "correlation_id": "",
            "delivery_plan": "",
            "delivery_task": "",
            "description": "",
            "due_date": "",
            "escalation": "0",
            "expected_start": "",
            "follow_up": "",
            "group_list": "",
            "hold_reason": "",
            "impact": "low",
            "incident_state": "1",
            "knowledge": "false",
            "location": "",
            "made_sla": "true",
            "notify": "1",
            "number": "INC0010125",
            "opened_at": "2025-05-07 11:37:15",
            "opened_by": "775acf7e83812210a6565929feaad3f0",
            "order": "",
            "origin_id": "",
            "origin_table": "",
            "parent": "",
            "parent_incident": "",
            "priority": "5",
            "problem_id": "9",
            "reassignment_count": "0",
            "reopen_count": "0",
            "reopened_by": "",
            "reopened_time": "",
            "resolved_at": "",
            "resolved_by": "",
            "rfc": "",
            "route_reason": "",
            "severity": "3",
            "short_description": "Refresh All UAT Databases from PROD",
            "sla_due": "",
            "state": "new",
            "subcategory": "",
            "sys_class_name": "incident",
            "sys_created_by": "aes.creator",
            "sys_created_on": "2025-05-07 11:37:15",
            "sys_domain": "global",
            "sys_domain_path": "/",
            "sys_id": "3b5aab66835d2250a6565929feaad36b",
            "sys_mod_count": "1",
            "sys_tags": "",
            "sys_updated_by": "aes.creator",
            "sys_updated_on": "2025-05-07 11:44:15",
            "task_effective_number": "INC0010125",
            "time_worked": "",
            "universal_request": "",
            "upon_approval": "proceed",
            "upon_reject": "cancel",
            "urgency": "low",
            "user_input": "",
            "watch_list": "",
            "work_end": "",
            "work_start": ""
        },
        "warnings": [
            "Encountered unknown value  while mapping field close_code.",
            "Encountered unknown value  while mapping field close_code.",
            "Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code.",
            "Encountered unknown value  while mapping field close_code."
        ]
    }
}

TASK [Cleanup the Temp Refresh Files | cleanup oracle_schema_refresh] ********************************************************************************************************************************************
changed: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Refresh Task SYS_ID Purged from Schedule and Ansible CTRL+ Disk"
}

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Has Now Closed the RFRSH2025Q2 Omni-Client PROD_2_UAT Task as Successful. Thank You and Have a Nice Day."
}

PLAY RECAP *******************************************************************************************************************************************************************************************************
localhost                  : ok=8    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible is Entering the Following in the Closing Notes (RFRSH2025Q2 Omni-Client PROD_2_UAT Task)"
}

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "closing_notes_out": {
        "changed": true,
        "diff": {
            "after": {
                "active": "true",
                "activity_due": "",
                "additional_assignee_list": "",
                "approval": "not requested",
                "approval_history": "",
                "assigned_to": "",
                "assignment_group": "",
                "attachments": [],
                "business_duration": "",
                "business_impact": "",
                "business_stc": "",
                "calendar_duration": "",
                "calendar_stc": "",
                "caller_id": "6816f79cc0a8016401c5a33be04be441",
                "category": "inquiry",
                "cause": "",
                "caused_by": "",
                "child_incidents": "0",
                "close_code": "",
                "close_notes": "Closed",
                "closed_at": "",
                "closed_by": "",
                "comments": "",
                "contact_type": "",
                "contract": "",
                "correlation_display": "",
                "correlation_id": "",
                "delivery_plan": "",
                "delivery_task": "",
                "description": "",
                "due_date": "",
                "escalation": "0",
                "expected_start": "",
                "follow_up": "",
                "group_list": "",
                "hold_reason": "",
                "impact": "low",
                "incident_state": "1",
                "knowledge": "false",
                "location": "",
                "made_sla": "true",
                "notify": "1",
                "number": "INC0010126",
                "opened_at": "2025-05-07 14:34:15",
                "opened_by": "775acf7e83812210a6565929feaad3f0",
                "order": "",
                "origin_id": "",
                "origin_table": "",
                "parent": "",
                "parent_incident": "",
                "priority": "5",
                "problem_id": "9",
                "reassignment_count": "0",
                "reopen_count": "0",
                "reopened_by": "",
                "reopened_time": "",
                "resolved_at": "",
                "resolved_by": "",
                "rfc": "",
                "route_reason": "",
                "severity": "3",
                "short_description": "Refresh All UAT Databases from PROD",
                "sla_due": "",
                "state": "new",
                "subcategory": "",
                "sys_class_name": "incident",
                "sys_created_by": "aes.creator",
                "sys_created_on": "2025-05-07 14:34:15",
                "sys_domain": "global",
                "sys_domain_path": "/",
                "sys_id": "58e218ba83116250a6565929feaad379",
                "sys_mod_count": "1",
                "sys_tags": "",
                "sys_updated_by": "aes.creator",
                "sys_updated_on": "2025-05-07 14:34:40",
                "task_effective_number": "INC0010126",
                "time_worked": "",
                "universal_request": "",
                "upon_approval": "proceed",
                "upon_reject": "cancel",
                "urgency": "low",
                "user_input": "",
                "watch_list": "",
                "work_end": "",
                "work_start": ""
            },
            "before": {
                "active": "true",
                "activity_due": "",
                "additional_assignee_list": "",
                "approval": "not requested",
                "approval_history": "",
                "assigned_to": "",
                "assignment_group": "",
                "attachments": [],
                "business_duration": "",
                "business_impact": "",
                "business_stc": "",
                "calendar_duration": "",
                "calendar_stc": "",
                "caller_id": "6816f79cc0a8016401c5a33be04be441",
                "category": "inquiry",
                "cause": "",
                "caused_by": "",
                "child_incidents": "0",
                "close_code": "",
                "close_notes": "",
                "closed_at": "",
                "closed_by": "",
                "comments": "",
                "contact_type": "",
                "contract": "",
                "correlation_display": "",
                "correlation_id": "",
                "delivery_plan": "",
                "delivery_task": "",
                "description": "",
                "due_date": "",
                "escalation": "0",
                "expected_start": "",
                "follow_up": "",
                "group_list": "",
                "hold_reason": "",
                "impact": "low",
                "incident_state": "1",
                "knowledge": "false",
                "location": "",
                "made_sla": "true",
                "notify": "1",
                "number": "INC0010126",
                "opened_at": "2025-05-07 14:34:15",
                "opened_by": "775acf7e83812210a6565929feaad3f0",
                "order": "",
                "origin_id": "",
                "origin_table": "",
                "parent": "",
                "parent_incident": "",
                "priority": "5",
                "problem_id": "9",
                "reassignment_count": "0",
                "reopen_count": "0",
                "reopened_by": "",
                "reopened_time": "",
                "resolved_at": "",
                "resolved_by": "",
                "rfc": "",
                "route_reason": "",
                "severity": "3",
                "short_description": "Refresh All UAT Databases from PROD",
                "sla_due": "",
                "state": "new",
                "subcategory": "",
                "sys_class_name": "incident",
                "sys_created_by": "aes.creator",
                "sys_created_on": "2025-05-07 14:34:15",
                "sys_domain": "global",
                "sys_domain_path": "/",
                "sys_id": "58e218ba83116250a6565929feaad379",
                "sys_mod_count": "0",
                "sys_tags": "",
                "sys_updated_by": "aes.creator",
                "sys_updated_on": "2025-05-07 14:34:15",
                "task_effective_number": "INC0010126",
                "time_worked": "",
                "universal_request": "",
                "upon_approval": "proceed",
                "upon_reject": "cancel",
                "urgency": "low",
                "user_input": "",
                "watch_list": "",
                "work_end": "",
                "work_start": ""
            }
        },
        "failed": false,
        "record": {
            "active": "true",
            "activity_due": "",
            "additional_assignee_list": "",
            "approval": "not requested",
            "approval_history": "",
            "assigned_to": "",
            "assignment_group": "",
            "attachments": [],
            "business_duration": "",
            "business_impact": "",
            "business_stc": "",
            "calendar_duration": "",
            "calendar_stc": "",
            "caller_id": "6816f79cc0a8016401c5a33be04be441",
            "category": "inquiry",
            "cause": "",
            "caused_by": "",
            "child_incidents": "0",
            "close_code": "",
            "close_notes": "Closed",
            "closed_at": "",
            "closed_by": "",
            "comments": "",
            "contact_type": "",
            "contract": "",
            "correlation_display": "",
            "correlation_id": "",
            "delivery_plan": "",
            "delivery_task": "",
            "description": "",
            "due_date": "",
            "escalation": "0",
            "expected_start": "",
            "follow_up": "",
            "group_list": "",
            "hold_reason": "",
            "impact": "low",
            "incident_state": "1",
            "knowledge": "false",
            "location": "",
            "made_sla": "true",
            "notify": "1",
            "number": "INC0010126",
            "opened_at": "2025-05-07 14:34:15",
            "opened_by": "775acf7e83812210a6565929feaad3f0",
            "order": "",
            "origin_id": "",
            "origin_table": "",
            "parent": "",
            "parent_incident": "",
            "priority": "5",
            "problem_id": "9",
            "reassignment_count": "0",
            "reopen_count": "0",
            "reopened_by": "",
            "reopened_time": "",
            "resolved_at": "",
            "resolved_by": "",
            "rfc": "",
            "route_reason": "",
            "severity": "3",
            "short_description": "Refresh All UAT Databases from PROD",
            "sla_due": "",
            "state": "new",
            "subcategory": "",
            "sys_class_name": "incident",
            "sys_created_by": "aes.creator",
            "sys_created_on": "2025-05-07 14:34:15",
            "sys_domain": "global",
            "sys_domain_path": "/",
            "sys_id": "58e218ba83116250a6565929feaad379",
            "sys_mod_count": "1",
            "sys_tags": "",
            "sys_updated_by": "aes.creator",
            "sys_updated_on": "2025-05-07 14:34:40",
            "task_effective_number": "INC0010126",
            "time_worked": "",
            "universal_request": "",
            "upon_approval": "proceed",
            "upon_reject": "cancel",
            "urgency": "low",
            "user_input": "",
            "watch_list": "",
            "work_end": "",
            "work_start": ""
        },
        "warnings": [
            "Encountered unknown value  while mapping field close_code.",
            "Encountered unknown value  while mapping field close_code.",
            "Encountered unknown value 2025Q5 Refreshed (Omni-CLient UAT) while mapping field close_code.",
            "Encountered unknown value  while mapping field close_code."
        ]
    }
}

TASK [Cleanup the Temp Refresh Files | cleanup oracle_schema_refresh] ********************************************************************************************************************************************
changed: [localhost]

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Refresh Task SYS_ID Purged from Schedule and Ansible CTRL+ Disk"
}

TASK [debug] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ansible Has Now Closed the RFRSH2025Q2 Omni-Client PROD_2_UAT Task as Successful. Thank You and Have a Nice Day."
}

PLAY RECAP *******************************************************************************************************************************************************************************************************
192.168.1.108              : ok=8    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
192.168.1.64               : ok=7    changed=3    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
localhost                  : ok=17   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[ansible_admin@ctrl ansible]$ 





