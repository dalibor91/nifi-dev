## NiFi demo

This repo contains NiFi demo template that you can use to test out NiFi. It's for developement purposes and it doesn't use ZooKeeper but runs standalone NiFi instance inside docker container. 

Dependencies 
- Docker

Best if you run this where `bash` availible (like MacOS or Linux based systems) since `./bin/` contains scripts in `bash` 


### Starting NiFI

When you are running first time this to start nifi instance run 

```bash 
docker-compose up
```

It will take few seconds but after some time you will have NiFi running at `https://localhost:8443/nifi`. To log in in this UI you need to find username and password that were generated for you in `./nifi/logs/` directory. Simple command for that 

```bash 
grep -r 'Generated Username' -A 1 ./nifi/logs
```

If you have multiple of this, please ignore everything in `./nifi/logs/.trash` because this directory holds old configs.

<details>
  <summary>Directory structure of the project</summary>

  Structure 
    - `nifi` contains all the nifi related things
      - `database_repository` is internal nifi directory that holds settings from your nifi setup
      - `flowfile_repository` is internal nifi directory that holds flow files 
      - `keystore` is directory that holds keystore which we use to store certificates 
        - `KeyStore.jks` has password `test123` and holds certificate to 
          - domain 1 
          - domain 2 
      - `logs` holds logs of nifi app that is running inside container 
      - `.trash` backup of old configurations
    - `templates` Templates used 
    - `data` Output processed by NiFi 
</details>


### Stopping Nifi 

If you wish to stop your nifi instance you can simply run 
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

