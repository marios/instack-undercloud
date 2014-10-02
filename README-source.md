instack-undercloud via source
=============================

1. The user performing all of the installation steps on the virt host needs to
   have password-less sudo enabled. This step is NOT optional, you must create an
   additional user. Do not run the rest of the steps as root.

         sudo useradd stack
         sudo passwd stack  # specify a password
         echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
         sudo chmod 0440 /etc/sudoers.d/stack
         sudo su - stack
         echo 'export LIBVIRT_DEFAULT_URI="qemu:///system"' >> ~/.bashrc
         source ~/.bashrc

1. Create initial directory for instack, and clone the needed repositories.


         mkdir instack
         cd instack
         git clone https://github.com/agroup/instack-undercloud
         git clone https://git.openstack.org/openstack/tripleo-incubator

1. Complete the initial setup.

         source instack-undercloud/instack-sourcerc
         tripleo install-dependencies
         tripleo set-usergroup-membership


1. Verify membership in the libvirtd group

         # verify you are in the libvirtd group
         id | grep libvirtd
         # if not, start a new shell to pick it up
         sudo su - stack
         cd instack
         source instack-undercloud/instack-sourcerc


1. Create the virtual environment. When the script has completed successfully,
it will output the instack vm's IP address. Use this IP address in the next
step.

         instack-virt-setup

1. ssh as the stack user (password is stack) to the instack vm

1. Download all the files from http://file.rdu.redhat.com/~jslagle/tripleo-images-juno-source/
   to /home/stack. If you already have them downloaded, use rsync/scp/whatever
   to copy them over to the instack vm. The images will be uploaded to glance
   at the end of the install.

1. Clone instack-undercloud, source instack-sourcerc, and run script to install the undercloud from
   source. The script will produce a lot of output on the sceen. It also logs to
   ~/.instack/install-undercloud.log. You should see `install-undercloud
   Complete!` at the end of a successful run.

        git clone https://github.com/agroup/instack-undercloud
        source instack-undercloud/instack-sourcerc
        instack-install-undercloud-source

1. Once the install script has run to completion, copy the files
   `/root/stackrc` and `/root/tripleo-undercloud-passwords` into the stack user's home directory.

         sudo cp /root/tripleo-undercloud-passwords .
         sudo cp /root/stackrc .

That completes the Undercloud install. To proceed with deploying and using the
Overcloud see [Overcloud-source](Overcloud-source.md).
