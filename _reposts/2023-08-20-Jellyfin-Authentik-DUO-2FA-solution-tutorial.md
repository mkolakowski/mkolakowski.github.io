---
title: "Jellyfin, Authentik, DUO. 2FA solution tutorial"
collection: reposts
permalink: /reposts/2023-08-20-Jellyfin-Authentik-DUO-2FA-solution-tutorial
excerpt: 'This tutorial/ method is 100% compatible with all clients. Has no redirects. when logging into jellyfin via through any client, etc. TV, Phone, Firestick and more.'
date: 2023-08-20
venue: 'Jellyfin Forums'
paperurl: 'https://forum.jellyfin.org/t-jellyfin-authentik-duo-2fa-solution-tutorial'
citation: 'HazzaFTW28. (2023, August 20). Jellyfin, authentik, duo. 2FA solution tutorial. https://forum.jellyfin.org/t-jellyfin-authentik-duo-2fa-solution-tutorial '
---

2023-08-20, 06:18 PM
reddit version: [https://www.reddit.com/r/selfhosted/comm..._tutorial/](https://www.reddit.com/r/selfhosted/comments/15wfmaz/jellyfin_authentik_duo_2fa_solution_tutorial/)


This tutorial/ method is 100% compatible with all clients. Has no redirects. when logging into jellyfin via through any client, etc. TV, Phone, Firestick and more, you will get a notification on your phone asking you to allow or deny the login.
for people who want more of an understanding of what it does, here's a video: [https://imgur.com/a/1PesP1D]([url](https://imgur.com/a/1PesP1D))
The following tutorial will done using a Debain/Ubuntu system but you can switch out commands as you need.
This quite a long and extensive tutorial but dont be intimidated as once you get going its not that hard.
credits to:
- LDAP setup: https://www.youtube.com/watch?v=RtPKMMKRT_E
- DUO setup: https://www.youtube.com/watch?v=whSBD8YbVlc&t

## Prerequisites:
1. Have your a public DNS record set to point to the authentik server. im using auth.YourDomainName.com.
2. Server to run you docker containers
3. Create a DUO admin account here: [https://admin.duosecurity.com]([url](https://admin.duosecurity.com))
4. When first creating an account, it will give you a free trial for a month which gives you the ability to add more than 10 users but after that you will be limited to 10.

## Install Authentik.
1. Install Docker:
```
sudo apt install docker docker.io docker-compose
```
2. give docker permissions:
```
sudo groupadd docker
sudo usermod -aG docker $USER
```
3. logout and back in to take effect
4. install secret key generator:
```
sudo apt-get install -y pwgen
```
5. install wget:
```
sudo apt install wget
```
6. get file system ready:
```
sudo mkdir /opt/authentik

sudo chown -R $USER:$USER /opt/authentik/
cd /opt/authentik/
```
7. Install authenik:
```
wget https://goauthentik.io/docker-compose.yml
echo "PG_PASS=$(pwgen -s 40 1)" >> .env
echo "AUTHENTIK_SECRET_KEY=$(pwgen -s 50 1)" >> .env
docker-compose pull
docker-compose up -d
```
8. Your server shoudl now be running, if you haven't mad any changes you can visit authentik at:
_http://<your server's IP or hostname>:9000/if/flow/initial-setup/
_

9. Create a sensible username and password as this will be accessible to the public.



## configure Authentik publicly.
At this step i would recommend you have your authentik server pointed at your public dns server. (cloudflare). if you would like a tutorial to simlulate having a static public ip with ddns & cloudflare message me.
- Once logged in, click Admin interface at the top right.
- On the left, click Applications > Outposts.
- You will see an entry called authentik Embedded Outpost, click the edit button next to it.
- change the authentik host to: authentik_host: https://auth.YourDomainName.com/
- click Update


### configure LDAP:
- On the left, click directory > users
- Click Create
- Username: service
- Name: Service
- click on the service account you just created.
- then click set password. give it a sensible password that you can remember later
- on the left, click directory > groups
- Click create
- name: service
- click on the service group you just created.
- at the top click users > add existing users > click the plus, then add the service user.
- on the left click flow & stages > stages
- Click create
- Click identification stage
- click next
- Enter a name: ldap-identification-stage
- Have the fields; username and email selected
- click finish
- again, at the top, click create
- click password stage
- click next
- Enter a name: ldap-authentication-password
- make sure all the backends are selected.
- click finish

- at the top, click create again
- click user login stage
- enter a name: ldap-authentication-login
- click finish
- on the left click flow & stages > flows
- at the top click create
- name it: ldap-athentication-flow
- title: ldap-athentication-flow
- slug: ldap-athentication-flow
-  designation: authentcation
- (optional) in behaviour setting, tick compatibility mode
- Click finish
- in the flows section click on the flow you just created: ldap-athentication-flow
- at the top, click on stage bindings
- click bind existing stage
- stage: ldap-identification-stage
- order: 10
- click create
- click bind existing stage
- stage: ldap-authentication-login
- order: 30
- click create
- click on the ldap-identification-stage > edit stage
- under password stage, click ldap-authentication-password
- click update

### allow LDAP to be queried
- on the left, click applications > providers
- at the top click create
- click LDAP provider
- click next
- name: LDAP
- Bind flow: ldap-athentication-flow
- search group: service
- bind mode: direct binding
- search mode direct querying
- click finish
- on the left, click applications > applications
- at the top click create
- name: LDAP
- slug: ldap
- provider: LDAP
- click create
- on the left, click applications > outposts
- at the top click create
- name: LDAP
- type: LDAP
- applications: make sure you have LDAP selected
- click create.
You now have an LDAP server. lets create a Jellyfin user and Jellyfin admin group.

### Jellyfin users
- jellyfin admins must be assigned to the user and admin group. normal user just assign to jellydin users
- on the left click directory > groups
- create 2 groups, Jellyfin Users & Jellyfin Admins. (case sensitive)
- on the left click directory > users
- create a user
- click on the user you just created and give it a password and assign it to the Jellyin User group. also add it to the Jellyfin admin group if you want

### Setup jellyfin for LDAP
- open you jellyfin server
- click dashboard > plugins
- click catalog and install the LDAP plugin
- you may need to restart.
- click dashboard > plugins > LDAP

## LDAP bind

- LDAP Server: the authentik servers local ip
- LDAP Port: 389
- LDAP Bind User: cn=service,ou=service,dc=ldap,dc=goauthentik,dc=io
- LDAP Bind User Password: (the service account password you create earlier)
- LDAP Base DN for searches: dc=ldap,dc=goauthentik,dc=io
- click save and test LDAP settings
- LDAP Search Filter:
  ```
  (&(objectClass=user)(memberOf=cn=Jellyfin Users,ou=groups,dc=ldap,dc=goauthentik,dc=io))
  ```
- LDAP Search Attributes: uid, cn, mail, displayName
- LDAP Username Attribute: name
- LDAP Password Attribute: userPassword
- LDAP Admin Filter:
  ```
  (&(objectClass=user)(memberOf=cn=Jellyfin Admins,ou=groups,dc=ldap,dc=goauthentik,dc=io))
  ```
- under jellyfin user creation tick the boxes you want.
- click save
- Now try to login to jellyfin with a username and password that has been assigned to the jellyfin users group.

## Bind DUO to LDAP
- In authentik admin click flows & stages > flows
- click default-authentication-flow
- at the top click stage binding
- you will see an entry called: default-authentication-mfa-validation, click edit stage
- make sure you have all the device classes selected
- not configured action: Continue
- on the left, click flows & stages > flows
- at the top click create
- Name: Duo Push 2FA
- title: Duo Push 2FA
- designation: stage configuration
- click create
- on the flow stage, click the flow you just created: Duo Push 2FA
- at the click stage bindings
- click create & bind stage
- click duo authenticator setup stage
- click next
- name: duo-push-2fa-setup
- authentication type: duo-push-2fa-setup
- you will need to fill out the 3 duo api fields.
- login to DUO admin: https://admin.duosecurity.com/
- in duo on the left click application > protect an application
- find duo api > click protect
- you will find the keys you need to fill in.
- configuration flow: duo-push-2fa
- click next
- order: 0
- click flows & stages > flows
- click ldap-athentication-flow
- click stage bindings
- click bind existing stage
- name: default-authentication-mfa-validation
- click update

## LDAP will now be configured with DUO. to add user to DUO, go to the DUO
- click users > add users
- give it a name to match the jellyfin user
- down the bottom, click add phone. this will send the user a text to download DUO app and will also include a link to active the the user on that duo device.
- when in each users profile in DUO you will see a code embedded in URL. something like this; "[https://admin-11111.duosecurity.com/user...8RY4R78Y13](https://admin-11111.duosecurity.com/users/DNEF78RY4R78Y13)"
- you want to copy that code on the end.
- in authentik navigate to flows & stages > stages
- find the duo-push-2fa slow you created but dont click on it.
- next to it there will be a actions button on the right. click it to bring up import device
- select the user you want and the map it to the code you copied earlier.

now whenever you create a new user, create it in authentik and add the user the jellyfin users group and optionally the jellyfin admins group. then create that user in duo admin. once created get the users code from the url and assign it to the user in duo stage, import device option.
i hope this helps someone and do not hesitate to ask for help.
