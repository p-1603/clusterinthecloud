# clusterinthecloud

## Official Documentation

Basic CITC setup:
https://cluster-in-the-cloud.readthedocs.io/en/latest/index.html

Amber Installation:
https://ambermd.org/Manuals.php

## Setting up the Cluster

1) Install `terraform` version specified in CITC docs (not necessarily the latest version!).
2) Run `terraform init oracle` to install providers.
3) Set up `terraform.tfvars` in the `terraform` directory with all the correct OCIDs and keys. Terraform likes its private keys in `ssh-rsa` format.
4) Run `terraform apply oracle`.
5) Log into the cluster with the IP provided by `terraform apply oracle` as user `citc`. Set up `limits.yaml` with the compute shapes you want. See the available shapes at https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm.
6) Once the setup has completed, you should be able to run `finish` to set the compute nodes up. ***
7) Add users via the command `/usr/local/sbin/add_user_ldap username firstname lastname publickey`.
8) Et voila. Use `sinfo` or `list_nodes` to see the nodes and check slurm is working.

*** This step is currently broken and the setup does not complete, due to a faulty installaion of `libpmi`. A way around this problem is to comment out the `- security_udpates` role line in `/home/citc/citc-ansible/management.yml` and run `/home/citc/run_ansible` manually. It will hang at "Wait for packer to finish", but the setup will otherwise be complete, and you can CTRL+C here. Create the empty `mgmt` file in `/mnt/shared/finalised/` manually and you will then be able to run `finish`.
