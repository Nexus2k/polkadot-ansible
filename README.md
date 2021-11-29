# polkadot-ansible
Ansible scripts to deploy a Polkadot/Kusama validator node.

## Getting started

Prerequisits:

*  VPS or dedicated server meeting the Polkadot/Kusama requirements (you might want to have a beefier machine as we're going to run two validators on one machine)
*  ansible (2.11.6+)
*  ansible-playbook (2.11.6+)

First copy the `hosts.ini-sample` to `hosts.ini` then:

1.  Rename the two `my_node_*` entries to a name you like, also replace the `<IP of your server>` with the actual IP of your server
1.  Copy/Rename `host_vars/my_node_*.yml` files to the same name you just called the host in the `hosts.ini` file.
1.  Update the values in the `host_vars/my_node_*.yml` files, you always want to:
  1.  Update the `node_name` this is the node name your validator will show up with in the Telemetry dashboards
  1.  Update the `polkadot_db_snapshot_url` and `polkadot_db_snapshot_checksum` with the latest values from https://polkashots.io/
  1.  Update your `stash_addr` with your stash address. (If you don't know what a stash address is you probably don't want to run your own validator!)