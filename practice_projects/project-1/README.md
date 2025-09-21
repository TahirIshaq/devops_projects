# Objective
1. Install NGINX
2. Host a "Hello World" website on NGINX
3. Create a local ANAME record to access the website

# Implementation

## Website creation
```
echo "Hello World!" > index.html
```

## Install NGINX
Docker will be used to install NGINX.
```
docker pull nginx:1.29.0-bookworm
```

## Host the website
The default configuration uses `.conf` for additonal configuration. And in this case, just the `index.html` was replaced. To use a customized configuration file mount the `.conf` file to `/etc/nginx/conf.d/website_name.conf` and the website files to whatever the path has been mentioned in the configuration file. Forward the default http port to `8080` on the host system.

```
docker run -d -p 8080:80 -v ./index.html:/usr/share/nginx/html/index.html nginx:1.29.0-bookworm

# Test the website is working
curl localhost:8080
```

## Add a local DNS record
Add the desired DNS record locally.
```
echo -e "127.0.0.1\thello.com" | sudo tee /etc/hosts
# Test the website
curl hello.com:8080
```

## Cleanup
Stop and remove nginx container
```
# Assuming there is only 1 nginx container
docker stop $(docker ps -aq  --filter ancestor=nginx:1.29.0-bookworm)
docker rm $(docker ps -aq  --filter ancestor=nginx:1.29.0-bookworm)
```