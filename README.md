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

# SSL Certificates
As described above, we're using Cloudflare's origin server certificates. To create a new origin server certificate, navigate to your Cloudflare dashboard under `SSL/TLS` and click `Origin Server`, then `Create Certificate`. The certificate should contain two hostnames by default. `example.com` and `*.example.com` (It shows your domain name instead of example.com). Leave everything as it is, make the certificate valid for 15 years and click `Create`. After you've successfully generated the certificate, store the certificate as `example.com.crt` and the key as `example.com.key`. Those have to be uploaded to the `certs` folder on your server.
