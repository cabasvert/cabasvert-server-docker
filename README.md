## Run the dev image

First copy the `server-config.json.example` file as `server-config.json` and edit it as required.
For instance (remember the browser client app is hosted by the server app):
```json
{
  "port": 8080,
  "clientApplication": {
    "url": "https://app.example.com"
  },
  "serverApplication": {
    "url": "https://app.example.com"
  },
  "database": {
    "url": "https://db.example.com",
    "auth": {
      "username": "server-user-username",
      "password": "server-user-password"
    }
  },
  "email": "email@example.com",
  "smtpConnection": {
    "host": "smtp.example.com",
    "port": 587,
    "secure": true,
    "auth": {
      "username": "email-username",
      "password": "email-password"
    }
  }
}
```

Then make a docker secret of it (you need to be in swarm mode):

```
docker secret create server-config server-config.json
```

Finally pull the image and run it:

```
docker pull cabasvert/cabasvert-server:latest
docker service create --name cabasvert-server -p 80:8080 \
  --secret source=server-config,target=server-config.json,uid=1000,gid=1000,mode=0400 \
  -e SERVER_CONFIG_FILE=/run/secrets/server-config.json \
  cabasvert/cabasvert-server:latest
```
