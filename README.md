# OpenLDAP Playground
A Dockerized OpenLDAP Server Playground with Practical Examples and Guides.





## OpenLDAP Setup Using Docker Compose

Using Docker Compose simplifies the process of setting up an OpenLDAP server. Please refer to the `docker-compose.yml` file for more details.

### Main Environment Variables
```yaml
# LDAP Configuration
LDAP_ORGANISATION: 'Example'
LDAP_DOMAIN: 'example.com'
LDAP_ADMIN_USERNAME: 'admin'
LDAP_ADMIN_PASSWORD: 'example.com'
LDAP_CONFIG_PASSWORD: 'example.com'
```
````yaml
LDAP_BACKEND: "mdb"
````
- `LDAP_BACKEND`: Specifies the database backend for LDAP.


### Connection Test
You can test your LDAP server connection using the following command:
```bash
ldapsearch -x -H ldap://localhost:389 -D "cn=admin,dc=example,dc=com" -w example.com -b "dc=example,dc=com"
```
- `-x`: Utilizes Simple authentication, avoiding the complexity of SASL (Simple Authentication and Security Layer). Generally used for testing or simpler tasks.

- `-H ldap://localhost:389`: Provides the LDAP server's URL, directing the command to connect to localhost on port 389, the default port for LDAP.

- `-D "cn=admin,dc=example,dc=com"`: Represents the "Bind DN," specifying which user will log into the LDAP server. In this case, it is the admin account.

- `-w example.com`: Specifies the password for the Bind DN, which is example.com in this case. For security reasons, it is generally recommended to read the password from a file.

- `-b "dc=example,dc=com"`: Indicates the "Base DN," serving as the starting point for the search within the LDAP tree, set here as dc=example,dc=com.


### Recommended DB for OpenLDAP
OpenLDAP supports various backend databases, with the most commonly used being mdb and hdb (the successor to bdb). Each backend has its own pros and cons depending on specific requirements.

- `mdb`: Fast, simple, and relatively low-resource. Recommended for most new projects.
- `hdb/bdb`: Previously widely used in older versions of OpenLDAP. Provides transaction and recovery features but can be more complex to configure and may perform less efficiently than mdb.


The mdb backend is generally recommended for contemporary use.

### Data persistence
Data persistence ensures that data is retained even after a container is stopped or deleted. In OpenLDAP, persistence is achieved by storing the database (/var/lib/ldap) and configuration (/etc/ldap/slapd.d) on the host system.

```shell
sudo mkdir -p /data/slapd/config
sudo mkdir /data/slapd/database
```
Set the right permissions.
```
sudo chmod 775 -R /data/slapd
sudo chown -R $USER:docker /data/slapd
```
After the above configuration is complete, the volumes can be defined and used as follows.
```dockerfile
    volumes:
      - ./data/certificates:/container/service/slapd/assets/certs
      - ./data/slapd/database:/var/lib/ldap
      - ./data/slapd/config:/etc/ldap/slapd.d
```

## Organization Unit
In LDAP (Lightweight Directory Access Protocol), an Organizational Unit (OU) serves as a container concept that facilitates the creation of a hierarchical structure within the directory service.

An OU is utilized to categorize and manage various objects within the directory, such as users, groups, computers, and other entities.

### Roles of OU
- Logical Structuring : Enables logical grouping and hierarchical organization of objects within the directory.

- Access Control : Enhances security by allowing or restricting access permissions specifically for certain OUs.

- Policy Implementation : Allows the application of specific policies to an OU, enabling consistent settings and restrictions for all objects residing under it.

### Prerequisites for Using OUs in LDAP
It's important to note that LDAP employs a hierarchical structure, requiring a parent object to exist when creating a new object.

Failing to meet this requirement will lead to errors such as "No Such Object."


If you're planning to create users and groups, you should pre-define the appropriate OUs as follows. (Refer to ldif/create_ou.ldif for more details)
```bash
dn: ou=users,dc=example,dc=com
objectClass: organizationalUnit
ou: users
description: Users Organizational Unit

dn: ou=groups,dc=example,dc=com
objectClass: organizationalUnit
ou: groups
description: Groups Organizational Unit
```