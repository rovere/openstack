0. Key into concepts about cloud and openstack at CERN [here](https://clouddocs.web.cern.ch/clouddocs/overview/concepts.html)

1. Edit the files ```devel-openstack-vm-userdata_shell.txt``` and ```devel-openstack-vm-userdata_cloud-config.txt``` adding/modifying items according to your needs: packages to be installed in the VM you're creating, commands/options executed in the VM creation 

2. run the [write-mime-multipart](http://manpages.ubuntu.com/manpages/trusty/man1/write-mime-multipart.1.html) which will produce ```devel-openstack-vm-userdata_combined.txt```, merging the information of the two editable files above
```
write-mime-multipart --output=devel-openstack-vm-userdata_combined.txt devel-openstack-vm-userdata_shell.txt:text/x-shellscript devel-openstack-vm-userdata_cloud-config.txt
```

3. Create an ssh key and load it with a name, so it's know to openstack, see [instructions](https://clouddocs.web.cern.ch/clouddocs/using_openstack/keypair_options.html)

4. Decide which [image](https://clouddocs.web.cern.ch/clouddocs/details/standard_images.html) and  [flavor](https://clouddocs.web.cern.ch/clouddocs/using_openstack/vm_flavors.html) you want to use

5. Creating a new VM logging into the ```ssh lxplus-cloud``` cluster and customising this command with your ingredients:

```
nova boot --image "SLC6 CERN Server - x86_64 [2014-08-05]" --flavor m1.large --key_name mr-slc5_64-dev --user_data devel-openstack-vm-userdata_combined.txt mrovere-quat-slc6
```
