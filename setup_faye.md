Start with a [Basic server setup](https://github.com/loomio/loomio/wiki/Basic-VPS-setup) with [a deploy user added](https://github.com/loomio/loomio/wiki/Add-a-deploy-user-to-your-host). Give this host a domain record such as: `faye.loomio.example.com`


Login to you faye server

```
ssh deploy@faye.loomio.example.com
mkdir tls
```

Copy across your SSL certificate files into `/home/deploy/tls`
From your local machine
```
scp your_private.key deploy@faye.loomio.example.com:cert/server.key
scp certificate_chain.pem deploy@faye.loomio.example.com:cert/server.pem
```

Install docker. This command adds the latest docker apt repositories to the system and installs docker via apt.

On the faye server
```
curl -sSL https://get.docker.io/ubuntu/ | sudo sh
```

Set PRIVATE_PUB_SECRET_TOKEN on your dokku.

dokku config:set loomio PRIVATE_PUB_SECRET_TOKEN=`openssl rand -base64 48`
dokku config:set loomio FAYE_URL=https://faye.loomio.org:4443/faye

Run the image
```
sudo docker run -p 4443:4443 \
                -e PRIVATE_PUB_SECRET_TOKEN=your_private_pub_secret_token \
                -v /path/to/your/cert/files:/home/app/cert \
                --name loomio-faye-server -d robguthrie/loomio-faye-server

sudo docker run robguthrie/loomio-faye-server -v tls:/home/app/tls -e PRIVATE_PUB_SECRET_TOKEN=something
docker run -p 4443:4443 -e PRIVATE_PUB_SECRET_TOKEN=`openssl rand -base64 48` -v /Users/rob/projects/loomio-faye-server/cert:/home/app/cert loomio-faye-server 
```


