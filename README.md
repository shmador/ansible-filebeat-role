# ansible-filebeat-role

A role to install, configure, and manage Filebeat (the Elastic log shipper) on Debian-based hosts. It installs the package from Elastic’s APT repo, deploys a custom `filebeat.yml` (with date-stamped indices by default), and handles service restarts whenever the config changes.

## Requirements

- Ansible 2.9+  
- Debian-family Linux (uses `apt_key`, `apt_repository`, `apt`)  
- Network access to the Elastic APT repository and Elasticsearch hosts  
- (Optional) A running Kibana instance if you enable dashboard loading  

## Role Variables

All variables have sensible defaults in `defaults/main.yml`, but can be overridden:

| Variable              | Default                                                                                   | Description                                                                                        |
|-----------------------|-------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| `filebeat_version`    | `"7.17.0"`                                                                                | The Filebeat package version to install.                                                          |
| `log_paths`           | `["/var/log/*.log"]`                                                                      | List of paths for Filebeat to harvest.                                                            |
| `elastic_apt_repo`    | `"deb https://artifacts.elastic.co/packages/7.x/apt stable main"`                         | URL for the Elastic APT repository (vars/main.yml).                                                |
| `elastic_gpg_key_url` | `"https://artifacts.elastic.co/GPG-KEY-elasticsearch"`                                    | URL for the Elastic GPG key.                                                                       |
| `elastic_hosts`       | `["51.17.138.28:9200", "51.84.12.187:9200", "51.17.8.194:9200"]`                           | List of Elasticsearch hosts.                                                                       |
| `index_name`          | `"index-%{+yyyy.MM.dd}"`                                                                  | Index name pattern (used when ILM is disabled).                                                   |
| `template_name`       | `"index"`                                                                                 | Name of the Elasticsearch index template.                                                          |
| `template_pattern`    | `"index-*"`                                                                               | Index template pattern.                                                                            |
| `kibana_host`         | `"http://51.17.8.194:5601"`                                                               | URL of the Kibana instance (for dashboard loading).                                                |

## Dependencies

None. This is a standalone role. If you wish to integrate with other roles (e.g. an Elasticsearch or Kibana role), be sure to configure `elastic_hosts` and `kibana_host` accordingly in your playbook.

## Example Playbook

```yaml
- hosts: logging
  become: true

  vars:
    filebeat_version: "7.17.3"
    elastic_hosts:
      - "es1.example.com:9200"
      - "es2.example.com:9200"
    index_name: "logs-%{+yyyy.MM.dd}"
    template_name: "logs"
    template_pattern: "logs-*"
    kibana_host: "https://kibana.example.com:5601"

  roles:
    - shmador.ansible-filebeat-role
```

This will:

1. Add Elastic’s APT key and repo  
2. Install Filebeat version 7.17.3  
3. Deploy `/etc/filebeat/filebeat.yml` with your custom index and template settings  
4. Restart Filebeat whenever the template or index pattern changes  

## License

BSD  

## Author Information

- **Shmador** – [GitHub](https://github.com/shmador)  
- **Dor** – Maintainer of the Elastic Ansible collection on GitHub  

Feel free to open issues or submit pull requests on the [role’s GitHub page](https://github.com/shmador/ansible-filebeat-role) for improvements or bug reports.
