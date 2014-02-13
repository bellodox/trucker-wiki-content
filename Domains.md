# Domains

This guide will learn you how to assign custom domain names to your apps.

## DNS Records
Set the following records for your custom domain:

```bash
www   CNAME gate.ie.trucker.io.
*     A     54.194.83.183
```

Then add the domain and routes to your App with truck:

```bash
truck map-domain customdomain.com
truck map myApp customdomain.com
truck map --app myApp --host www --domain customdomain.com
truck routes # Shows your routing setup
```

Please note that it may take a while for the new DNS settings to be in effect. Once the custom domain points to the new IP it will show the trucker app.
