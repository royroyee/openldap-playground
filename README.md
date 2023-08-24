# OpenLDAP Playground
A Dockerized OpenLDAP Server Playground with Practical Examples and Guides.





## Docker Compose


### Data persistence
Data persistence ensures that data is retained even after a container is stopped or deleted. In OpenLDAP, persistence is achieved by storing the database (/var/lib/ldap) and configuration (/etc/ldap/slapd.d) on the host system.

```shell
sudo mkdir -p /data/slapd/config
sudo mkdir /data/slapd/database
```
Set the right permissions.
````
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

### OpenLDAP env
```


```