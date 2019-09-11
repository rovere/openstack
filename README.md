# Notes to create an openstack VM at CERN
#### inputs from @rovere @deguio @cerminar @franzoni

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

setenv OS_AUTH_URL https://keystone.cern.ch/main/v2.0
setenv OS_USERNAME `id -un`
setenv OS_TENANT_NAME "Personal $OS_USERNAME"


nova boot --image "CC7 - x86_64 [2018-12-03]" --flavor m2.small --key-name gf-VM-key-2019-09-09  --user-data devel-openstack-vm-userdata_combined.txt   gf-vm-slc7-c
```

6. once the VM is up and runnng, ```cvmfs``` is active yet not fully configured (which leands to the wrong value of the soft link ```/cvmfs/cms.cern.ch/SITECONF/local```, which crashes cmsRun when trying to access global tag from a CERN-located VM ) 
```
edit in the file 
/etc/cvmfs/config.d/cms.cern.ch.conf

export CMS_LOCAL_SITE
to
export CMS_LOCAL_SITE=T2_CH_CERN
```
