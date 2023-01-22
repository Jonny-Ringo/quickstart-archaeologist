## Setup Instructions

### Prerequisites:
- Running a server with the following installed:
  - git
  - [docker (>= 20.0)](https://www.simplilearn.com/tutorials/docker-tutorial/how-to-install-docker-on-ubuntu)
  - [docker compose (>= 2.0.0)](https://docs.docker.com/compose/install/linux/#install-the-plugin-manually)
  - Minimum requirements: 4GB hard drive space, 512MB RAM
  - An example of a server with these prerequisites already installed: [https://marketplace.digitalocean.com/apps/docker](https://marketplace.digitalocean.com/apps/docker)
- ETH wallet (private key) with:
  - ETH balance (for signing transactions)
  - SARCO balance (for bonding your archaeologist to curses)
- BIP39 compatible mnemonic (generate a new one here: [https://iancoleman.io/bip39/](https://iancoleman.io/bip39/))
- RPC URL (Infura, Alchemy, etc.)

_If running on Goerli (chain id = 5) or Sepolia (chain id = 11155111), then you will need Goerli/Sepolia ETH + Goerli/Sepolia SARCO._

- A registered domain name [pointed at your server's ip address](https://www.servers.com/support/knowledge/dedicated-servers/how-to-point-your-domain-name-to-dedicated-servers-ip-address#:~:text=To%20point%20your%20domain%20name%20to%20your%20dedicated%20server's%20public,on%20the%20domain's%20name%20servers.) 
---

### Initial Setup Instructions

1. Clone this repo:

   `git clone https://github.com/sarcophagus-org/quickstart-archaeologist`

2. Copy example env file.

   `cp .env.example .env`

3. Fill out the env file values.
- To generate a BIP39 seed offline, run: `COMPOSE_PROFILES=seed docker compose run seed-gen`

4. Create blank peer ID file.

   `touch peer-id.json`

5. **If you have not yet registered your archaeologist:**

   > `COMPOSE_PROFILES=register docker compose run register`  
   
   _(or `docker-compose` for older versions of docker compose)_

   Follow the instructions to register your archaeologist. A peer ID will automatically be generated for you.

   ---
   
   **Notes on Registration:**
   - It is recommended you set your free bond to at least a 10x multiple of your digging fee. (I.e. if `MIN_DIGGING_FEE=5`, set your `FREE_BOND` to at least `50`.
   - When your free bond drops below your minimum digging fee, you will no longer be able to accept new jobs or appear in the embalmer web application list.
   - See CLI instructions for updating these values after registration.

6. Run the service in the background

   > `COMPOSE_PROFILES=service docker compose up -d`
   
7. You can verify your service is registered correctly by visiting https://dev-sarcophagus.netlify.app/archaeologists
- Please allow up to a minute for the archaeologist list to populate.

### Logging
To view the logs

Run `docker container ls` and grab the container ID of the archaeologist service

Run `docker logs <container-id> --follow` to see realtime logs

### CLI
A CLI is provided for running additional commands for your service, such as updating profile values and claiming rewards.

To run the CLI: 
1. If the service is not started, start the service with `docker compose up -d`,
2. Jump into the container with: `docker compose exec -it archaeologist sh`
3. Run `cli help` for available commands, or `cli help <command>` for help with a given command.

##### Example
**Update Digging Fee to 5**
```
docker compose exec -it archaeologist sh
cli update -d 5
exit
```

**Deposit 100 SARCO to free bond**
```
docker compose exec -it archaeologist sh
cli update -f 100
exit
```

**View Profile**
```
docker compose exec -it archaeologist sh
cli view -p
exit
```

### Updating the service
To update the service to the latest version:<br>
```
COMPOSE_PROFILES=service docker compose stop
COMPOSE_PROFILES=service docker compose pull
COMPOSE_PROFILES=service docker compose up -d
```

### Troubleshooting
Below are some answers to common problems when setting up the service.

**I registered the archaeologist, but it is not showing in the web application**
1. Make sure your free bond is larger than your minimum digging fee. You will not show up in the web application if you do not have enough free bond posted to accept new jobs.
2. Ensure your domain is pointed at the IP address of your server using: https://www.nslookup.io/website-to-ip-lookup
3. See if any errors appear in the logs of either your archaeologist service, or the SSL service

```
**Archaeologist Logs**
docker container ls   // get container ID of ghcr.io/sarcophagus-org/sarcophagus-v2-archaeologist-service:latest
docker logs <container_id>
```

```
**SSL cert logs**
docker container ls   // get container ID of nginxproxy/acme-companion
docker logs <container_id>
```
