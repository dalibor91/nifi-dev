## NiFi development

This repo contains NiFi demo template that you can use to test out NiFi. It's for developement purposes and it doesn't use ZooKeeper but runs standalone NiFi instance inside docker container. 

Dependencies 
- Docker

### Starting NiFI

When you are running first time this to start nifi instance run 

```bash 
docker-compose up
```

It will take few seconds but after some time you will have NiFi running at `https://localhost:8443/nifi`. To log in in this UI you need to find username and password that were generated for you in `./nifi/logs/` directory. Simple command for that 

```bash 
grep -r 'Generated Username' -A 1 ./nifi/logs
```

If you have multiple of this, only last entrance should be used.

<details>
  <summary>Directory structure of the project</summary>

  Structure 
    - `nifi` contains all the nifi related things
      - `keystore` is directory that holds keystore which we use to store certificates 
        - `keystore.jks` has password `test123` and holds certificates
      - `logs` holds logs of nifi app that is running inside container 
    - `templates` Templates used 
    - `data` Output processed by NiFi 
</details>


### Stopping Nifi 

If you wish to stop your NiFi instance you can simply run 
```bash 
docker-compose down
```

If you wish to remove all the file related to instance 

```bash 
bin/cleanup
```

### Templates 
There is already prebuilt template in `./templates` directory that you can use to create test flows. Importing of templates is done 

Import template to system 

Apply template 

Fix Services 


### Importing SSL certificates

So if we want to enable HTTP processors - specifically GetHTTP we need to setup SSL Context Service. 
Lets say for example we want to enable this for `www.ecb.europa.eu` first thing that you would want is download certificate for this domain. That can be done by using 

```bash
openssl s_client -connect www.ecb.europa.eu:443
```

certificate looks something like 

```txt
-----BEGIN CERTIFICATE-----
            ...
            ...
-----END CERTIFICATE-----
```

To make this much easier if you want to save certificate to `nifi/keystore/certificate.cer` you can do that from browser also by visiting that url or you can manually copy/paste it.

<!-- ```bash
openssl s_client \
    -connect www.ecb.europa.eu:443 \
    -servername ecb.europa.eu 2>&1 \
    | awk '/BEGIN CERTIFICATE/,/END CERTIFICATE/' > nifi/keystore/certificate.cer
``` -->

Once you have certificate set up, you can enter container that is running NiFi by using 

```bash 
docker-compose exec nifi bash 
```

Access `keystore` directory where newly created certificate should be stored, and also `keystore.jks`

```bash 
cd keystore
```

And you can run following command to import certificate 

```bash 
keytool -import -alias ecb_europa_eu -file ./certificate.cer -keystore ./keystore.jks -storepass test123
```

Then inside Nifi Flow Configuration page, under Controller Services, find your StandardSSLContextService and change following properties
- Keystore Filename - `/opt/nifi/nifi-current/keystore/keystore.jks`
- Keystore Password - `test123`
- Keystore Type - `JKS`
- Truststore Filename - `/opt/nifi/nifi-current/keystore/keystore.jks`
- Truststore Password - `test123`
- Truststore Type - `JKS`
- TLS Protocol - `TLS`

And save it

