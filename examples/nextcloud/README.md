# Webdav Header
In order for Nextcloud to function properly you will need to add this middleware to your dynamic configuration file:

```yaml
http:
    middlewares:
        nextcloud-headers:
            headers:
                browserXssFilter: true
                contentTypeNosniff: true
                frameDeny: true
                sslRedirect: true
                stsIncludeSubdomains: true
                stsPreload: true
                stsSeconds: 31536000
                customFrameOptionsValue: "SAMEORIGIN"
```
