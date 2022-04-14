# About this Guide
Welcome to this Traefik quick-start guide. It has come to life to make setting up Traefik less of a hassle if you're a first time adopter. It explains the very basics of how Traefik operates and the minimum setup required to get it running further down this guide. I highly encourage you to read the entire guide as it will give you a good head-start and make configuring Traefik a fun and simple experience.

# The (official) Documentation
Reading the official documentation over at [Traefik Labs](https://doc.traefik.io/traefik/) can be very frustrating if you have never worked with Traefik before or you're just starting out. However, you will still need the official documentation to customize your Traefik installation to your needs and infrastructure as this guide does not cover every possible configuration option available. This guide is meant as a quick-start guide to quickly get a Traefik instance up and running and does not ship a fully production-ready configuration that magically works for every setup.

# Minimum Requirements
* [Docker](https://docs.docker.com/engine/install/) running on a server
* [Docker Compose](https://docs.docker.com/compose/install/) installed on that server
* Ports `80`, `443` and `8080` (all TCP) opened in the firewall (If you are running the server behind a firewall.)

# What this Guide includes
This guide will explain to you how you can set up Traefik on a single instance/server, that is running Docker. It does **not include** the setup of Let's Encrypt certificats using the ACME challenge (yet). *(Might add it later, depends on the demand for it.)* The certificates used in this guide are Cloudflare origin server certificates using SSL strict mode enabled in the Cloudflare dashboard. You can find more information about that under [SSL Certificates](#ssl-certificates).

# How Traefik operates
As this guide is a very barebones introduction to Traefik, we will be breaking it down into four major pieces (in that order):
1. Entrypoints
2. Routers
3. Middleware
4. Services

## 1. Entrypoints
Entrypoints basically define what ports your Traefik instance is listening on. The majority of users won't have to change this, unless you want to expose something that is not listening to either port `80` or `443`. Entrypoints are added in the `static configuration`.

## 2. Routers
Routers are basically a set of rules, instructing Traefik what to do with the request, once it has passed through on of the entrypoints. One of those rules could be the URL that your service is listening to. Like `blog.example.com` or maybe a path, like `/blog` to load your blog.

## 3. Middleware
Middleware is what the name suggests. They sit between the incoming request and the service. With Middleware you can manipulate requests, like the headers that are being send and other information that comes with sending a request to the server.

## 4. Service
Each request must eventually be handled by a service. Services must have a "target" which is basically just the container that should receive the request. Services are configured differently depending on your `providers`. More on providers down below.

# Providers
Providers are the the technologies that Traefik connects to. You can run Traefik using multiple providers at once. In this guide, we've configured Traefik using the Docker provider and the file provider. It is basically where Traefik pulls its information from. In our case it's the Docker socket and the dynamic configuration file. Providers are configured in the `static configuration` file.

# Configuring in labels
Traefik allows you to configure it in multiple ways. We're going for the `configuration in labels`. That means that we will be attaching labels to all of our containers that interact with Traefik. Another way would be to write the entire configuration yourself in the `dynamic configuration` file, but that kind of goes against this guide, which aims to make the configuration simple and fun and not more stressful.

## Pros and Cons (Labels vs Dynamic Configuration)
The pros and cons of both of those methods are pretty simple to sum up. Using labels makes your docker-compose files much more readable and easier to migrate to a new system. Services will configure themselves, however you'll miss out in terms flexibility a little bit as you have to restart the container every time you make changes to the labels. Writing everything in the dynamic configuration file makes your code a horror to read and maintain but you will be able to make changes to your containers without having to restart them. Services have to be manually configured and ports also have to be mapped manually.

# The configuration Files
In this guide we will be shipping Traefik with two configuration files. Both of which are important and serve a very specific purpose. Feel free to modify them to your needs and requirements. More information about the configuration parameters can be found here:<br>
[Configuration overview](https://doc.traefik.io/traefik/getting-started/configuration-overview/)

| Reference (Traefik docs) | File in repository | File will be read ... | Specialties | Notes |
| :----------------------- | :----------------- | :-------------------- | :---------- | :---- |
| [Static configuration](https://doc.traefik.io/traefik/getting-started/configuration-overview/#the-static-configuration) | config/traefik.yml | on Traefik start | - | Changes require a restart |
| [Dynamic configuration](https://doc.traefik.io/traefik/getting-started/configuration-overview/#the-dynamic-configuration) | config/dynamic.yml | during runtime | Changes will be reflected live | Syntactical errors break Traefik |

# SSL Certificates
As described above, we're using Cloudflare's origin server certificates. To create a new origin server certificate, navigate to your Cloudflare dashboard under `SSL/TLS` and click `Origin Server`, then `Create Certificate`. The certificate should contain two hostnames by default. `example.com` and `*.example.com` (It shows your domain name instead of example.com). Leave everything as it is, make the certificate valid for 15 years and click `Create`. After you've successfully generated the certificate, store the certificate as `example.com.crt` and the key as `example.com.key`. Those have to be uploaded to the `certs` folder on your server.

# Deploy on your Server
To deploy the files of this repository to your server you can just go ahead and download the latest version as a `.zip`-File and extract it on your server. The static configuration can more or less be left "as is", as none of what is configured there is critical for later use. You should put your focus on the dynamic configuration file instead. Most of the configuration like middleware and certificates are configured there.

## Install SSL Certificates
The SSL certificates have to be uploaded to the `certs` folder on the server. A good habit is to call them like your domain name including the top level domain and it's extension at the end. That would be `.crt` for certificate files and `.key` for the key files. After you're done uploading the certificates to your server, all you have to do is add them to the dynamic configuration file that is provided with this repository. Traefik will pick up your certs automatically, once you have added them to the dynamic configuration file.

## HTTP to HTTPS redirects
This part might be a little confusing at first but you can not redirect to HTTPS immediately. You will have to configure your services to listen to the entrypoint on port `:80` first and pass the connection onto your HTTPS entrypoint on port `:443`. We're doing this by putting a middleware on our connection over port 80 that will automatically redirect us to port 443. From there on we will enable TLS so Traefik can search our certs folder and pick the correct certificate accordingly.

## Create a network
For Traefik to be able communicate with your containers you need to put them all into the same network. Docker Compose creates networks by default, however they are not persistent when you take down your docker-compose.yml file. That's why we will need to create our own network. This is very simple. All you have to do is run the following command: `docker network create proxy` This will create a new network called `proxy` using the `bridge` driver. We will be adding all of our containers to this network.

### Join the network
For your containers to now be able to be picked up by Traefik you need to join them into the same network. You can easily do so by putting this at end of your compose file: 
```yaml
networks:
  proxy:
    external: true
```
On your service, you tell it to join the network:
```yaml
networks:
  - proxy
```

## Run a container
That was all we had to do already. We are now ready and prepared to run containers and proxy them through Traefik! Create a new docker-compose.yml file or use one of the example files from the `examples` folder. We will be explaining what most of the parameters in the `docker-compose.yml` actually mean and do.

### Labels (The most important)
It would be beneficial for you to understand what you're actually configuring, that's why we'll walk you through the most important labels that you can configure in your compose files:

```yaml
labels:
  - "traefik.enable=true" # Tells Traefik to proxy this container. If set to false, Traefik will ignore this container. Useful for databases that run behind websites and don't have to be exposed to the public.
  - "traefik.docker.network=proxy" # The network Traefik uses to proxy connections. Should always be set to the network that Traefik is part of. MUST be set when your container is part of multiple networks or else Traefik will pick a random network every time you restart the container
```

# Proxy Webinterface - default Credentials
Traefik comes with a nice looking and very informative webinterface. It does not let you configure anything through this interface but we still strongly advise against make it publicly accessible anyways. That's why with our configuration it comes with a http-auth by default. The default username is `proxy` and the default password is `changeme`. You can change the details, or add new users in the `config/dynamic.yml` file. Use this [generator](https://www.web2generators.com/apache-tools/htpasswd-generator) to create new credentials.

# Feedback
Found a typo, got something to add to this guide or ideas for improvement?<br>
Open an issue and we'll get it fixed! Thank you very much in advance!
