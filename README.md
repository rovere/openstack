1. Edit the files devel-openstack-vm-userdata_shell.txt and
   devel-openstack-vm-userdata_cloud-config.txt according to your needs
1. run the command 
```
write-mime-multipart --output=devel-openstack-vm-userdata_combined.txt devel-openstack-vm-userdata_shell.txt:text/x-shellscript devel-openstack-vm-userdata_cloud-config.txt
```

1. use this customization while creating a new VM:

```
nova boot --image "SLC6 CERN Server - x86_64 [2014-08-05]" --flavor m1.large --key_name mr-slc5_64-dev --user_data devel-openstack-vm-userdata_combined.txt mrovere-quat-slc6
```
