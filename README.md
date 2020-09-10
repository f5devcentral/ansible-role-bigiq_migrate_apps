# Ansible Role: bigiq_migrate_apps

Performs a series of steps needed to Migrate AS3 application service(s) with its referenced objects from a BIG-IP to another BIG-IP.

*Example: Migration from a BIG-IPs on Premise to Public Cloud. Migration from legacy BIG-IP platforms to new platforms.*

**Limitations/Prerequisites:**

- both devices need to be managed by BIG-IQ
- migration of AS3 application services only
- migration is per AS3 tenants
- migration of 1 tenant at a time
- app services have to be migrated to a **new tenant** (same tenant name not supported)
- AS3 ``/Common/shared`` tenant not supported
- referenced objects must be located in /Common and have a unique name across BIG-IQ
- referenced objects supported: 
  - SSL Certificate and Key (must be managed on BIG-IQ ([more info](https://techdocs.f5.com/en-us/bigiq-7-1-0/managing-big-ip-devices-from-big-iq/ssl-certificates.html)))
  - Security Logging Profile
  - WAF Policy

If you are interested for other type of objects, [open an issue on GitHub](https://github.com/f5devcentral/ansible-role-bigiq_migrate_apps/issues).

## Role Variables

Available variables are listed below. For their default values, see `defaults/main.yml`.

Establishes initial connection to your BIG-IQ. These values are substituted into
your ``provider`` module parameter. These values should be the connection parameters
for the **CM BIG-IQ** device.

        provider:
          user: admin
          server: 10.1.1.4
          server_port: 443
          password: secret
          loginProviderName: tmos
          validate_certs: no

Define the tenant containing the AS3 application service to migrate to the new BIG-IP device.

      # Working directory to store migration files on your local machine
      dir_as3: ~/tmp 
      
      tenant_to_migrate: datacenter1
      
      new_tenant_name: us-east-1
      
      # Name of the Application in BIG-IQ Dashboard which will contain the migrated App Services
      new_bigiq_app_name: "App Services migrated"
      
      # Target BIG-IP device, IP address only
      new_device_address: 10.1.1.7 

      # OPTIONAL: Replace virtual server within the tenant
      new_virtual_servers: 
        - { old: "10.1.10.101", new: "192.168.1.101" }
        - { old: "10.1.10.102", new: "192.168.1.102" }
        
      # false by default
      remove_old_tenant: false 

## Example Playbook

    ---
    - hosts: all
      connection: local
      vars:
        provider:
          user: admin
          server: "{{ ansible_host }}"
          server_port: 443
          password: secret
          loginProviderName: tmos
          validate_certs: no

      tasks:
          - name: Migrate AS3 application service(s) from a BIG-IP to another.
            include_role:
              name: f5devcentral.bigiq_migrate_apps
            vars:
              dir_as3: ~/tmp
              tenant_to_migrate: datacenter1
              new_tenant_name: us-east-1
              new_bigiq_app_name: "App Services migrated"
              new_device_address: 10.1.1.7
            register: status

## License

Apache

## Author Information

This role was created in 2020 by [Romain Jouhannet](https://github.com/rjouhann).

[1]: https://galaxy.ansible.com/f5devcentral/bigiq_pinning_deploy_objects

