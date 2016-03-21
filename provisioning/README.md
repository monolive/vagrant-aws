# ansible-hdp
Ansible recipe to install HDP ( Hortonworks Data Platform) 

The solution rely on ambari-shell. It will clone the repository
https://github.com/sequenceiq/ambari-shell.git and grab the latest available. 


It has currently being tested against:
 - AWS on CentOS7 with MariaDB


# How To 
Configure repositories and other variables in group_vars/all 

Add your blueprint in blueprints folder. You will also need to modify the host-mapping section to reflect your env.
The blueprint needs to be parameterised ( hostname / group / mount point) and copied into blueprint folder. It will be define in groups_var/all


In case of failure, you may want to delete ambari-server package / delete /tmp/blueprint.json and /tmp/host-map.json as if they are present steps are skipped

# TODO
- Implement postgresql
- Implement kerberos
- Implement Ranger
- Generate a host map based upon hosts config
