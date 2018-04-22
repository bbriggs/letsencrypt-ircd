## IRC Services using LetsEncrypt 

_A reference implementation for automatically deploying a full-featured IRC server with automatic SSL certificate management, Anope services, Kiwi IRC webchat, a TOR hidden service, and Privatebin thrown in for good measure_

## How to use this project

 1. Fork and clone it, then customize the following files to point at your servers/DNS names.
 2. Install [docker-compose](https://docs.docker.com/compose/install/)
 3. Create external network. `docker network create webproxy`
 4. `docker-compose up -d` and grab a drink. Anope takes about 5 minutes to compile. If you're on a slow connection, wait even longer. Some of these images are a tad hefty.
 5. Wait for letsencrypt to generate certificates for your external services. This typically takes another 2-3 minutes on the first run (ie, if `./data/certs` was empty)
 6. `docker logs -f nginx-letsencrypt` to make sure certs were properly created.

#### anope/conf/services.conf

 - line 3: set name 
 - line 4: set value
 - line 14: change to non-default password
 - line 19: change name
 - line 34: set networkname
 - line 61: set seed
 - lines 182-191: configure oper block
     - Optional: Add more opers
 - line 193: configure mail block

#### unrealircd/conf/unrealircd.conf

You should read this config file especially close as there are many defaults from the original UnrealIRCd configuration in here still.

 - line 60-63: change `me` block
 - line 68-72: admin block
 - line 146-154: oper block
     - Optional: Add more opers
 - line 178: services name in link block (should match services from anope)
 - line 188: anope services password
 - line 195: sasl block
 - line 231-247: OPTIONAL: link block (uncomment to use)
 - line 256: uline block
 - line 264-265: drpass block
 - line 367: vhost block
 - line 380-401: Network configuraiton
 - line 466-467: *Important: Your server will not work at all without this set!* Set hostname in cert to match hostname of your server in DNS

#### kiwiirc/config.json

 - The whole thing. No, really. It's short. Just edit the whole thing. You should leave line 4 alone, though. That needs to point at their server.
 - Startup options should point to your IRC server and channel of choice. Unless you have a websocket-compatible IRC server (you don't if you're using this stack), leave direct as `false`. 

#### docker-compose.yml

Some environment variables in containers are actually used to configure other containers. For example, VIRTUAL_HOST and LETSENCRYPT_* are used to set certificate names and vhosts. These should match what's configured for this server in DNS. If your DNS points to irc.example.com, then the vhost and letsencrypt value should as well.

 - line 63-65: vhost and letsencrypt values
 - line 113-115: vhost and letsencrypt values
 - line 133-135: vhost and letsencrypt values

### Questions

 - Q: Why didn't you just use an image for everything? Why are we compiling anope? 
   - Some people prefer to compile locally for images that aren't official from Docker Hub, because the Dockerfile may not match the actual uploaded image.
   - Some images are updated infrequently and this makes local modifications easier.
   - Others (nginx-proxy and nginx-letsencrypt) are sourced from _very_ popular and frequently updated images, so I left maintenance to them.
 - Q: I did everything you said but I can't connect to IRC!
   - Check that you're allowing these ports in your firewall
   - Make sure Letsencrypt generated the cert. You may need to restart the ircd and/or anope if this is your first run. The IRC server will start before the certificate is generated, causing a failure on the SSL listener. Anope will retry every 60 seconds automatically.
 - Q: Why Privatebin?
   - This is a reference implementation. If you don't want Privatebin, you can drop it. It's just there to demonstrate how easy it is to add a new service.
 - Q: Why Redis?
   - Anope writes to Redis. Anope is a pain to get to write to a local flatfile in this configuration. You should take care to make backups of `./redis-data`
   - I still had to configure a static IP for it because apparently m_redis can't accept hostnames as an argument. Only IP addresses. ¯\_(ツ)_/¯

#### Contributing

Submit a pull request or open an issue!
