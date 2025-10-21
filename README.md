# Ansible Role: Certkit.Sync

An Ansible Role for Linux that installs a script which synchronizes SSL certificates managed by [Certkit.io](https://www.certkit.io/).

## Requirements

A Certkit account ([Signup for a free](https://app.certkit.io/signup)).

## Installing the Role

Install using the `ansible-galaxy` CLI:

```
ansible-galaxy role install certkit.sync
```

## Role Variables

All variables are listed below. They are all required, unless otherwise specified:

- `certkit_bucket`: The name of your certkit storage bucket. Get this from the Certkit UI.
- `certkit_access_key`: The access key for your certkit storage bucket. Get this from the Certkit UI.
- `certkit_secret_key`: The secret key for your certkit storage bucket. Get this from the Certkit UI.
- `certkit_common_name`: The domain name of the certificate. Prefix with * if it's a wildcard.
- `certkit_dir`: The directory where the certkit sync script and config file will be placed. Arbitrary, pick what you'd like. Should be unique if multiple certkit scripts are installed on the same box!
- `certkit_update_cmd`: Certkit sync runs this command whenever the certificates are updated. Use to inform the server of a new certificate.
- `certkit_pem_destination`: File path where Certkit sync will write the certificate PEM file. This is wherever your server software expects the certificate to live.
- `certkit_key_destination`: File path where Certkit sync will save the certificate private key file. This is wherever your server software expects the certificate to live.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  tasks:

    - include_role:
        name: certkit.sync
      vars:
        # Credentials from the CertKit UI
        certkit_bucket: certkit-1234
        certkit_access_key: YOUR_ACCESS_KEY
        certkit_secret_key: YOUR_SECRET_KEY

        # This is the common name/domain of the certificate.  If it's wildcard, prefix with *.
        certkit_common_name: "*.yourdomain.com"

        # The directory where the certkit sync script and config file will be placed. Arbitrary, pick what you'd like.
        # When syncing multiple certificates, each configuration should use a different directory.
        certkit_dir: /opt/certkit-nginx

        # When certificates are updated, this command will be run, nginx in this case. See our examples for how to notify other services.
        # IMPORTANT! - You may need to specify the full path to the executable. In many distros, Cron has no PATH configured.
        certkit_update_cmd: "/usr/sbin/nginx -s reload"

        # The PEM and KEY files will be moved here (and kept up to date). Place wherever your software expects them to be.
        certkit_pem_destination: "/etc/nginx/yourdomain.pem"
        certkit_key_destination: "/etc/nginx/yourdomain.key"
```

## Syncing Multiple Certificates

Sync more than one certificate by simply calling the role again. These variables will differ between each certificate:
    - `certkit_common_name`
    - `certkit_dir`
    - `certkit_pem_destination`
    - `certkit_key_destination`

## License

MIT / BSD
