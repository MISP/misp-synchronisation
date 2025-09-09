# Setup a Forgejo Runner Locally to Run the MISP Pipeline

## 1. Start a Forgejo Server (if you don’t already have one)

Run the following command with the provided `docker-compose.yml` file:

```bash
docker compose up -d
```

```yaml
networks:
  forgejo:
    name: forgejo-network

services:
  forgejo:
    image: codeberg.org/forgejo/forgejo:1.21
    container_name: forgejo
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - FORGEJO__database__DB_TYPE=postgres
      - FORGEJO__database__HOST=db:5432
      - FORGEJO__database__NAME=forgejo
      - FORGEJO__database__USER=forgejo
      - FORGEJO__database__PASSWD=forgejo
      - FORGEJO__database__DISABLE_SSH=false
      - FORGEJO__server__DOMAIN=forgejo
      - FORGEJO__server__ROOT_URL=http://forgejo:3000
      - FORGEJO__server__BASE_URL=http://forgejo:3000
      - FORGEJO__webhook__ALLOWED_HOST_LIST=external,loopback,woodpecker-server
      - FORGEJO__actions__ENABLED=true
    restart: always
    networks:
      - forgejo
    volumes:
      - forgejo-data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "2222:22"
      - "3000:3000"
    depends_on:
      - db

  db:
    image: postgres:14
    restart: always
    environment:
      - POSTGRES_USER=forgejo
      - POSTGRES_PASSWORD=forgejo
      - POSTGRES_DB=forgejo
    networks:
      - forgejo
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  forgejo-data:
  postgres-data:
```

---

## 2. Deploy the Runner

1. Go to:  
   `https://forgejo.server/admin/actions/runners`

2. Click on **Create new Runner** and copy the registration token.

3. In a new terminal, run:

```bash
git clone https://code.forgejo.org/forgejo/lxc-helpers
sudo cp -a lxc-helpers/lxc-helpers{,-lib}.sh /usr/local/bin
lxc-helpers.sh lxc_container_create myrunner
lxc-helpers.sh lxc_container_start myrunner
lxc-helpers.sh lxc_container_user_install myrunner 1000 debian
```

4. Get the local IP address of `lxcbr0`:

```bash
ip a
```

---

## 3. Connect to the Runner

Open a terminal session and execute:

```bash
lxc-helpers.sh lxc_container_run forgejo-runners -- sudo --user debian bash
```

Then, inside the runner terminal, do:

```bash
sudo forgejo-runner register
```

Answer the prompts:

```
INFO Enter the Forgejo instance URL (for example, https://next.forgejo.org/): 
http://<lxcbr0_IP>:3000

INFO Enter the runner token: 
<REGISTRATION TOKEN>

INFO Enter the runner name (if set empty, use hostname: myrunner): 
runner-CI

INFO Enter the runner labels, leave blank to use the default labels (comma-separated, for example, ubuntu-20.04:docker://node:20-bookworm,ubuntu-18.04:docker://node:20-bookworm): 
lxc
```

The runner should now appear as **available** in your Forgejo server.

---

## 4. Clone Required Repositories

On your local Forgejo server, create a mirror clone of these GitHub repositories:

- [shivammathur/setup-php](https://github.com/shivammathur/setup-php.git)  
- [viper-framework/viper-test-files](https://github.com/viper-framework/viper-test-files.git)  

---

## 5. Add a GitHub Token (Optional)

To speed up cloning dependencies inside the pipeline, you can add your GitHub token here:  

```
https://forgejo.server/user/settings/actions/secrets
```

---
