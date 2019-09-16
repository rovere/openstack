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

export OS_AUTH_URL=https://keystone.cern.ch/main/v2.0
export OS_USERNAME=`id -un`
export OS_TENANT_NAME="Personal $OS_USERNAME"


nova boot --image "CC7 - x86_64 [2018-12-03]" --flavor m2.small --key-name gf-VM-key-2019-09-09  --user-data devel-openstack-vm-userdata_combined.txt   gf-vm-slc7-c
```

6. once the VM is up and runnng, ```cvmfs``` is active yet not fully configured (which leands to the wrong value of the soft link ```/cvmfs/cms.cern.ch/SITECONF/local```, which crashes cmsRun when trying to access global tag from a CERN-located VM - see [CERN IT snow ticket](https://cern.service-now.com/service-portal/view-request.do?n=RQF1403172) ) 
```
edit in the file 
/etc/cvmfs/config.d/cms.cern.ch.conf

export CMS_LOCAL_SITE
to
export CMS_LOCAL_SITE=T2_CH_CERN
```

7. Also the eos installation is not complete, by just execiting the commands ```locmap --enable eosclient; locmap --configure eosclient```, as reported in the [documenration]( https://cern.service-now.com/service-portal/article.do?n=KB0003846 ): the home directories ```ls -ltrFh /eos/home-f/franzoni``` are accessible, but not the content of ```/eos/cms```. Based on the [ticket](https://cern.service-now.com/service-portal/view-request.do?n=RQF1404375), I've had to additionally do:

```
---------------------------------------------------------------------------------------
[16:06:33 - 19-09-16]  /afs/cern.ch/user/f/franzoni % hostname
franzoni-slc7-b.cern.ch
[16:06:43 - 19-09-16]  /afs/cern.ch/user/f/franzoni % diff /etc/auto.eos  ~/auto.eos
[16:06:45 - 19-09-16]  /afs/cern.ch/user/f/franzoni % sudo /bin/systemctl restart autofs.service
[sudo] password for franzoni:
[16:07:08 - 19-09-16]  /afs/cern.ch/user/f/franzoni % lt /eos/cms
total 0
drwxr-xr-x. 1 5410 zh    16P May 18  2011 store/
drwxr-xr-x. 1 root root 162T Jul 28  2011 proc/
drwxr-xr-x. 1 root root    0 May 13  2014 cmst3/
drwxr-x---. 1 root zh      0 Mar 16  2015 tier0/
drwxr-xr-x. 1 8619 2688 9.7G Apr 23 13:17 opstest/
```
