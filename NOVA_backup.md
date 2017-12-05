### NOVA - backup

#### Take a backup of Nova instance


##### Initial state

    nova list
    +--------------------------------------+------------+--------+------------+-------------+---------------------+
    | ID                                   | Name       | Status | Task State | Power State | Networks            |
    +--------------------------------------+------------+--------+------------+-------------+---------------------+
    | 7961383e-7528-4e26-8889-ab452bae0e7f | admin_vm01 | ACTIVE | -          | Running     | net04=192.168.12.78 |
    +--------------------------------------+------------+--------+------------+-------------+---------------------+

##### Take a backup (1st)


    nova backup admin_vm01 admin_vm01_bkup daily 1
    nova help backup
    usage: nova backup <server> <name> <backup-type> <rotation>

##### Backup a server by creating a 'backup' type snapshot.


    Positional arguments:
      <server>       Name or ID of server.
      <name>         Name of the backup image.
      <backup-type>  The backup type, like "daily" or "weekly".
      <rotation>     Int parameter representing how many backups to keep around.

Note:

    <backup-type> is kind of TAG
    <rotation> is How many backups you want to keep
    Nova backup is not automatically backup function, it is just how many backups with having same TAG you want to keep.


##### Check the backup

Nova command

    nova image-list | egrep "ID|admin_vm01_bkup"
    | ID                                   | Name                                     | Status | Server                               |
    | 9b641354-3875-49c5-9cc0-3b722791ccab | admin_vm01_bkup                          | ACTIVE | 7961383e-7528-4e26-8889-ab452bae0e7f |

Glance command

    glance image-list | egrep "ID|admin_vm01_bkup"
    | ID                                   | Name                                     | Disk Format | Container Format | Size       | Status |
    | 9b641354-3875-49c5-9cc0-3b722791ccab | admin_vm01_bkup                          | raw         | bare             | 1073741824 | active |

    glance image-show admin_vm01_bkup
    +---------------------------------------+--------------------------------------+
    | Property                              | Value                                |
    +---------------------------------------+--------------------------------------+
    | Property 'backup_type'                | daily                                |
    | Property 'base_image_ref'             | a5232547-4ccf-4ebb-9ab8-79e4daf73e9d |
    | Property 'description'                | cirros-changed-password              |
    | Property 'image_location'             | snapshot                             |
    | Property 'image_state'                | available                            |
    | Property 'image_type'                 | backup                               |
    | Property 'instance_type_ephemeral_gb' | 0                                    |
    | Property 'instance_type_flavorid'     | 318a3051-4304-4f3a-bda7-6f3a7637152b |
    | Property 'instance_type_id'           | 2                                    |
    | Property 'instance_type_memory_mb'    | 256                                  |
    | Property 'instance_type_name'         | m1.nano                              |
    | Property 'instance_type_root_gb'      | 1                                    |
    | Property 'instance_type_rxtx_factor'  | 1.0                                  |
    | Property 'instance_type_swap'         | 0                                    |
    | Property 'instance_type_vcpus'        | 1                                    |
    | Property 'instance_uuid'              | 7961383e-7528-4e26-8889-ab452bae0e7f |
    | Property 'network_allocated'          | True                                 |
    | Property 'owner_id'                   | a8085d1871f847bdb906ef4a28d1bd48     |
    | Property 'user_id'                    | a1c2ec19759040e39f219fcf82028bf6     |
    | checksum                              | 070cf4308133712b8711fa797a0d1f5f     |
    | container_format                      | bare                                 |
    | created_at                            | 2015-02-17T03:17:26                  |
    | deleted                               | False                                |
    | disk_format                           | raw                                  |
    | id                                    | 9b641354-3875-49c5-9cc0-3b722791ccab |
    | is_public                             | False                                |
    | min_disk                              | 1                                    |
    | min_ram                               | 0                                    |
    | name                                  | admin_vm01_bkup                      |
    | owner                                 | a8085d1871f847bdb906ef4a28d1bd48     |
    | protected                             | False                                |
    | size                                  | 1073741824                           |
    | status                                | active                               |
    | updated_at                            | 2015-02-17T03:18:19                  |
    +---------------------------------------+--------------------------------------+

##### Take a backup (2nd)

    nova backup admin_vm01 admin_vm01_bkup daily 1
    nova help backup
    usage: nova backup <server> <name> <backup-type> <rotation>

Backup a server by creating a 'backup' type snapshot.

    Positional arguments:
      <server>       Name or ID of server.
      <name>         Name of the backup image.
      <backup-type>  The backup type, like "daily" or "weekly".
      <rotation>     Int parameter representing how many backups to keep around.
      
Note:

     <backup-type> is kind of TAG
     <rotation> is How many backups you want to keep
     Nova backup is not automatically backup function, it is just how many backups with having same TAG you want to keep.

##### Check the backup

Being Backup

Once the backup has been completed. 1st backup (9b641354-3875-49c5-9cc0-3b722791ccab) will be deleted. Because <rotation> is set "1".

    glance image-list
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+
    | ID                                   | Name                                     | Disk Format | Container Format | Size       | Status |
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+
    | 9b641354-3875-49c5-9cc0-3b722791ccab | admin_vm01_bkup                          | raw         | bare             | 1073741824 | active |
    | 7cd46251-edbd-444b-a34f-8369420b40ce | admin_vm01_bkup                          | raw         | bare             |            | queued |
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+
    Complete


1st backup (9b641354-3875-49c5-9cc0-3b722791ccab) has been deleted.


    glance image-list
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+
    | ID                                   | Name                                     | Disk Format | Container Format | Size       | Status |
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+
    | 7cd46251-edbd-444b-a34f-8369420b40ce | admin_vm01_bkup                          | raw         | bare             | 1073741824 | active |
    +--------------------------------------+------------------------------------------+-------------+------------------+------------+--------+

##### Restore from the backup (= Create new Nova instance)

Check necessary infomation

Network

    neutron net-list | egrep " id |net04"
    | id                                   | name           | subnets                                               |
    | b3fa9d4d-4c21-47f5-be07-cf2ff518035e | net04          | 77267538-9be9-47db-a983-9139de4efe50 192.168.12.0/24  |

Security Group


    nova secgroup-list
    +--------------------------------------+---------+-------------+
    | Id                                   | Name    | Description |
    +--------------------------------------+---------+-------------+
    | 319b5bc2-5df2-44c1-b2d9-8d08689e4a8a | default | default     |
    +--------------------------------------+---------+-------------+

Flavor


    nova flavor-list
    +--------------------------------------+---------------+-----------+------+-----------+------+-------+-------------+-----------+
    | ID                                   | Name          | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
    +--------------------------------------+---------------+-----------+------+-----------+------+-------+-------------+-----------+
    | 1                                    | m1.tiny       | 512       | 1    | 0         |      | 1     | 1.0         | True      |
    | 12d7efd2-2d1d-44ec-ad66-ce7ca8927c0b | m1.medium     | 4096      | 0    | 0         |      | 2     | 1.0         | True      |
    | 2                                    | m1.small      | 2048      | 20   | 0         |      | 1     | 1.0         | True      |
    | 318a3051-4304-4f3a-bda7-6f3a7637152b | m1.nano       | 256       | 1    | 0         |      | 1     | 1.0         | True      |
    | 4                                    | m1.large      | 8192      | 80   | 0         |      | 4     | 1.0         | True      |
    | 5                                    | m1.xlarge     | 16384     | 160  | 0         |      | 8     | 1.0         | True      |
    +--------------------------------------+---------------+-----------+------+-----------+------+-------+-------------+-----------+

###### Restore from the image(backup) (= Create new Nova instance)

    nova boot --flavor m1.nano \
            --image admin_vm01_bkup \
            --security-groups default \
            --availability-zone nova \
            --nic net-id=b3fa9d4d-4c21-47f5-be07-cf2ff518035e \
            admin_vm02

    +--------------------------------------+--------------------------------------------------------+
    | Property                             | Value                                                  |
    +--------------------------------------+--------------------------------------------------------+
    | OS-DCF:diskConfig                    | MANUAL                                                 |
    | OS-EXT-AZ:availability_zone          | nova                                                   |
    | OS-EXT-SRV-ATTR:host                 | -                                                      |
    | OS-EXT-SRV-ATTR:hypervisor_hostname  | -                                                      |
    | OS-EXT-SRV-ATTR:instance_name        | instance-00000444                                      |
    | OS-EXT-STS:power_state               | 0                                                      |
    | OS-EXT-STS:task_state                | scheduling                                             |
    | OS-EXT-STS:vm_state                  | building                                               |
    | OS-SRV-USG:launched_at               | -                                                      |
    | OS-SRV-USG:terminated_at             | -                                                      |
    | accessIPv4                           |                                                        |
    | accessIPv6                           |                                                        |
    | adminPass                            | Df9gLcC7nkHq                                           |
    | config_drive                         |                                                        |
    | created                              | 2015-02-17T03:02:25Z                                   |
    | flavor                               | m1.nano (318a3051-4304-4f3a-bda7-6f3a7637152b)         |
    | hostId                               |                                                        |
    | id                                   | 94c75110-54a7-408a-b7e6-13b6961617bf                   |
    | image                                | admin_vm01_bkup (7cd46251-edbd-444b-a34f-8369420b40ce) |
    | key_name                             | -                                                      |
    | metadata                             | {}                                                     |
    | name                                 | admin_vm02                                             |
    | os-extended-volumes:volumes_attached | []                                                     |
    | progress                             | 0                                                      |
    | security_groups                      | default                                                |
    | status                               | BUILD                                                  |
    | tenant_id                            | a8085d1871f847bdb906ef4a28d1bd48                       |
    | updated                              | 2015-02-17T03:02:25Z                                   |
    | user_id                              | a1c2ec19759040e39f219fcf82028bf6                       |
    +--------------------------------------+--------------------------------------------------------+


Check the restored Nova instance

    nova list
    +--------------------------------------+------------+--------+------------+-------------+---------------------+
    | ID                                   | Name       | Status | Task State | Power State | Networks            |
    +--------------------------------------+------------+--------+------------+-------------+---------------------+
    | 7961383e-7528-4e26-8889-ab452bae0e7f | admin_vm01 | ACTIVE | -          | Running     | net04=192.168.12.78 |
    | 94c75110-54a7-408a-b7e6-13b6961617bf | admin_vm02 | ACTIVE | -          | Running     | net04=192.168.12.79 |
    +--------------------------------------+------------+--------+------------+-------------+---------------------+

