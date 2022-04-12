# About this Guide
Welcome to this Traefik quick-start guide. It has come to life to make setting up Traefik less of a hassle if you're a first time adopter. It explains the very basics of how Traefik operates and the minimum setup required to get it running further down this guide. I highly encourage you to read the entire guide as it will give you a good head-start and make configuring Traefik a fun and simple experience.

# The (official) Documentation
Reading the official documentation over at [Traefik Labs](https://doc.traefik.io/traefik/) can be very frustrating if you have never worked with Traefik before or you're just starting out. However, you will still need the official documentation to customize your Traefik installation to your needs and infrastructure as this guide does not cover every possible configuration option available. This guide is meant as a quick-start guide to quickly get a Traefik instance up and running and does not ship a fully production-ready configuration that magically works for every setup.

# Minimum Requirements
* [Docker](https://docs.docker.com/engine/install/) running on a server
* [Docker Compose](https://docs.docker.com/compose/install/) installed on that server
* Ports `80`, `443` and `8080` (all TCP) opened in the firewall (If you are running the server behind a firewall.)
