## Galaxy Import

To import the role into Ansible Galaxy for the first time, you can use the following command:

```bash
export AG_TOKEN=...
export AG_USER=....
ansible-galaxy role import --token $AG_TOKEN --branch main --role-name molecule_openstack $AG_USER molecule_openstack
```

## Galaxy Delete

### Role

````shell
export AG_TOKEN=...
export AG_USER=....
ansible-galaxy role delete --token $AG_TOKEN $AG_USER molecule_openstack
````

## Galaxy Token
To get your Galaxy token, follow this url: https://galaxy.ansible.com/ui/token/


## Galaxy API

Set hostname of galaxy and token:

```shell
export AG_HOST=galaxy.ansible.com
export AG_TOKEN=...
```

Run Commands:

```shell Get Namespaces
curl -X 'GET' \
  "https://${AG_HOST}/api/v3/namespaces/?limit=2" \
  -H 'accept: application/json' \
  -H "Authorization: Token ${AG_TOKEN}"
```

```shell Get Imports
curl -X 'GET' \
  "https://${AG_HOST}/api/v1/imports?limit=10" \
  -H 'accept: application/json' \
  -H "Authorization: Token ${AG_TOKEN}"
```

```shell Get Namespace
export AG_NAMESPACE=eclipse_slm

curl -X 'GET' \
  "https://${AG_HOST}/api/v3/namespaces/${AG_NAMESPACE}/" \
  -H 'accept: application/json' \
  -H "Authorization: Token ${AG_TOKEN}"
```

```shell Import Role
export AG_NAMESPACE=eclipse_slm

curl -X 'POST' \
  "https://${AG_HOST}/api/v1/imports/" \
  -H 'accept: application/json' \
  -H "Authorization: Token ${AG_TOKEN}" \
  --data-raw '{"alternate_namespace_name":"eclipse_slm","alternate_role_name":"docker","github_reference":"main","github_repo":"slm-ansible-role-docker","github_user":"ipa-big", "alternate_clone_url": "https://github.com/eclipse-slm/slm-ansible-role-docker"}'
```

curl -v -X POST \
    -H 'Authorization: token YOURAPITOKEN' \
    -H 'Content-Type: application/json' \
    -d '{"github_user": "nahsi", "github_repo": "ansible-base-ubuntu", "alternate_clone_url": "https://github.com/nahsilabs/ansible-base-ubuntu" }' \
    https://galaxy.ansible.com/api/v1/imports/
