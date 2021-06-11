# clusterinthecloud

## Official Documentation

Basic CITC setup:
https://cluster-in-the-cloud.readthedocs.io/en/latest/index.html

Amber Installation:
https://ambermd.org/Manuals.php
https://ambermd.org/InstCentOS.php

## Setting up the Cluster

1) Install `terraform` version specified in CITC docs (not necessarily the latest version!).
2) Run `terraform init oracle` to install providers.
3) Set up `terraform.tfvars` in the `terraform` directory with all the correct OCIDs and keys. Terraform likes its private keys in `ssh-rsa` format.
4) Run `terraform apply oracle`.
5) Log into the cluster with the IP provided by `terraform apply oracle` as user `citc`. Set up `limits.yaml` with the compute shapes you want. See the available shapes at https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm.
6) Once the setup has completed, you should be able to run `finish` to set the compute nodes up. ***
7) Add users via the command `/usr/local/sbin/add_user_ldap username firstname lastname publickey`.
8) Et voilà. Use `sinfo` or `list_nodes` to see the nodes and check slurm is working.

*** This step is currently broken and the setup does not complete, due to a faulty installaion of `libpmi`. A way around this problem is to comment out the `- security_udpates` role line in `/home/citc/citc-ansible/management.yml` and run `/home/citc/run_ansible` manually. It will hang at "Wait for packer to finish", but the setup will otherwise be complete, and you can CTRL+C here. Create the empty `mgmt` file in `/mnt/shared/finalised/` manually and you will then be able to run `finish`.

## Installing Amber & OpenMPI

1) Upload the AmberTools tarball to `/mnt/shared/` and untar it. 
2) Install the dependencies listed under https://ambermd.org/InstCentOS.php. Also use `yum` to install `cmake`. 
3) From the Amber docs, the `yum` version of OpenMPI is known to be incompatible with Amber and must be installed manually. Untar the openMPI tarball in `/amber_src/AmberTools/src/`. Then, run `configure_openmpi`, making sure that you install it somewhere in `/mnt/shared/` so that all users have access to it.
4) Add OpenMPI's `bin` directory to the `PATH` and OpenMPI's `lib` directory to `LD_LIBRARY_PATH`.
5) Set the `-DMPI` flag in `amber_src/AmberTools/build/run_cmake` is set to `TRUE`. Run `run_cmake`.
6) Once the `cmake` build is complete, run `make install`.
7) Add the directory containing `amber.sh` to the `PATH` and `source` the `amber.sh` script.
8) Add the environment and path variables above to users' `.bashrc`and to `/home/citc/compute_image_extra.sh` (which builds the node images) to allow users to submit jobs without needing to faff with paths in their .slm files.
