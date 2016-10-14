# cups-papercut

### First, read PaperCut [TOS and EULA](https://www.papercut.com/products/ng/manual/common/topics/license.html). Only proceed if you agree to them.

## This role installs and configures PaperCut
* ***PLEASE NOTE*** This role will uninstall any PaperCut versions < 16.3 and >= 17 using an expect script.
* Installs Papercut MF by downloading the PaperCut installation - [16.3](https://cdn.papercut.com/files/mf/16.x/pcmf-setup-16.3.38126-linux-x64.sh).
* Creates a non-privileged account called `papercut`. The account must be `papercut` as mandated by their [manual](http://www.papercut.com/products/ng/manual/common/topics/install-linux.html). 
    * This account is given temporary passwordless sudo access for the duration of the role and then removed in cleanup tasks that are run always.
    * Resource usage limits for the account are also removed.
* Installs Papercut non-interactively.
* Copies over `server.properties` file if specified.
* If a backup of a Papercut db is specified (zip file), then it'll use it to restore the state using the steps stated [here](http://www.papercut.com/products/ng/manual/common/topics/sys-backups.html)
* If SSL Private Key and Public key are specified it'll create the necessary certificate and install it using an expect script as specified [here](http://www.papercut.com/products/ng/manual/common/topics/tools-ssl-key-generation.html#tools-ssl-import-key)

## Requirements 
* Ansible >= 2.1
* Guest machine: Debian
    - stretch
    - jessie
    - wheezy
* Guest machine: Ubuntu
    - xenial
    - trusty
    - precise

## Possible additional tasks that are not part of this role's responsibilities. 
* Opening the necessary PaperCut ports - 9191, 9192 through the firewall. 
	- If you'd like to use [debops.ferm](https://github.com/debops/ansible-ferm) you can use/modify `cups_papercut__debops_ferm_dependent_rules` (defined in defaults) to pass through to [debops.ferm](https://github.com/debops/ansible-ferm)
* Samba share of the client install files.
	- If you'd like to use [bertvv.samba](https://github.com/bertvv/ansible-role-samba) you can use/modify `cups_papercut_client_share_files__bertvv_samba_config` (defined in defaults) to pass through to [bertvv.samba](https://github.com/bertvv/ansible-role-samba).
	- If using another role, the location of the client install files are stored in `cups_papercut_client_share_files_location`.

### Default Variables that can be overridden or used as-is when using this role:
* `cups_papercut_acct_pwd`: Password for the `papercut` user account - Default=hashed value of `1234`  ***PLEASE CHANGE THIS***
* `cups_papercut_download_url`: the URL to download PaperCut from. Default=[PaperCut MF 16.3.38126](https://cdn.papercut.com/files/mf/16.x/pcmf-setup-16.3.38126-linux-x64.sh)
* `cups_papercut_acct_groups`: Groups that the `papercut` account should be aprt of - Default=None.
* `cups_papercut__debops_ferm_dependent_rules`: Default simple rules to open up ports (9191 and 9192) through firewall that can be referenced when using [debops.ferm](https://github.com/debops/ansible-ferm) role.
* `cups_papercut_ldap`: If LDAP is being used/involved. It'll especially be useful when trying to synchronize account information after a data backup - Default=False
* `cups_papercut_client_share_files_location`: This is the default location for PaperCut client install files. Default=`/home/papercur/client`
* `cups_papercut_client_share_files__bertvv_samba_config`: Default simple config to setup a public Samba share for the PaperCut Client Install files that can be referenced when using [bertvv.samba](https://github.com/bertvv/ansible-role-samba) role.
* `cups_papercut_fetch_previous_version_to_localhost_location`: If PaperCut is already installed this role reads the information off `/home/papaercut/server/version.txt` to see if it's < 16.3 or >=17. To process this version.txt it must first reside on the ansible controller/host machine. Therefore it's fethced and stored in the lcoation defined in this variable on the host computer. Default=`/tmp/cups_papercut_ansible_role/{{inventory_hostname}}/previous_papercut_info`
* `cups_papercut_pfx_password`: Password for the pfx password that'll be created as well as for the ssl keystore that'll be created thereafter - Default=Not Defined
* `cups_papercut_license_key_file`: The license key file to copy to `/home/papercut/server/application.license` - Default=Not Defined
* `cups_papercut_source_ssl_private_key_location`: The private key file that's needed to setup SSL in PaperCut - Default=Not Defined
* `cups_papercut_source_ssl_public_key_location`: The public key file that's needed to setup SSL in PaperCut - Default=Not Defined
* `cups_papercut_db_backup_file`: The database file to use to restore settings - Default=Not Defined
* `cups_papercut_server_properties_file`: The location to server.properties files to overwrite the current one - Default=Not Defined
* `cups_papercut_ldap`: When LDAP is defined and if it should perform [User-LDAP sync](https://www.papercut.com/products/ng/manual/common/topics/tools-server-command.html) in PaperCut - Default=False

### TODO
* Secondary Site Mode
* Secondary Print Server Mode
* Configure for Sandbox Mode Web Print 