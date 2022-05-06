# polkadot-ansible
Ansible scripts to deploy up to two Docker based polkadot validator nodes. No need for compiling and complicated dependency management :)

## Please Nominate or Tip Me!

* Polkadot: [12Dw4SzhsxX3fpDiLUYXm9oGbfxcbg1Peq67gc5jkkEo1TKr](https://polkadot.subscan.io/waiting/12Dw4SzhsxX3fpDiLUYXm9oGbfxcbg1Peq67gc5jkkEo1TKr) (🍁 HIGH/STAKE 🥩/HEL1 - 2.69% commission)
* Polkadot: [12bget8jJWnyjqYPiCwkXZjDh5tDBkta1WUcDYyndbXVDmQ1](https://polkadot.subscan.io/waiting/12bget8jJWnyjqYPiCwkXZjDh5tDBkta1WUcDYyndbXVDmQ1) (🍁 HIGH/STAKE 🥩/ZRH1 - 0.69% commission)
* Kusama: [DbRgw96nMQcFEFZWTLd6LSPNdh8u3NBuUDfAhDmB6UU8cJC](https://thousand-validators.kusama.network/#/leaderboard/DbRgw96nMQcFEFZWTLd6LSPNdh8u3NBuUDfAhDmB6UU8cJC) (🍁 HIGH/STAKE 🥩/HEL1 - 4.20% commission)
* Kusama: [HQuPha82sRy91zZn73XRGJVV3ernBh5HZKftUcoDT8CSUwK](https://thousand-validators.kusama.network/#/leaderboard/HQuPha82sRy91zZn73XRGJVV3ernBh5HZKftUcoDT8CSUwK) (🍁 HIGH/STAKE 🥩/ZRH1 - 10% commission)

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
1.  Place an exported `wallet.json` and `password` file in `roles/polkadot-claimer/files` adding a -kusama or -polkadot suffix to the file name.

## Deployment

If you feel adventerous you can deploy the whole server using:

```
$ ansible-playbook -i hosts.ini all.yml
```

This will execute the following roles:

* polkadot-setup
  *  Setup Docker
  *  Setup Journald
  *  Setup motd (message of the day)
* polkadot-restore-db
  *  Downloads the in the host_vars defined snapshot and unpacks it to the future `db_path`
* polkadot-validator
  *  Starts the polkadot/kusama validators as docker containers
* polkadot-claimer
  *  Copies the wallet.json & password files to the server
  *  Setups a cronjob which executes a docker container that claims the staking rewards
* polkadot-rotate-keys
  *  Rotates the session keys so you can use for the `SetSessionKeys` extrinsic
* watchtower (not run automatically, run using `ansible-playbook -i hosts.ini setup_watchtower.yml`)
  *  Sets up a watchtower container which monitors upstream container image repositories for new releases and upgrades automatically.

You can also run the individual roles using the `setup_*.yml` playbooks instead of `all.yml`.

### Polkadot upgrades

To upgrade to the latest Polkadot version you can simply restart the containers using the `polkadot-validator` playbook.

## Notes

Monitoring is not yet covered in these ansible playbooks. Do NOT run a Polkadot/Kusama validator w/o proper monitoring or you will get slashed.

You should not use `root` user on the server, instead replace the `ansible_user` field in `hosts.ini` with an unpriviledged user (which has docker rights).
