---
title: 'Fail2Ban unraid-webgui cloudflare tunnel setup'
collection: posts
date: 2024-03-05
permalink: /posts/2024-06-08-Fail2Ban-Unraid-webgui-cloudflare-tunnel-setup
tags:
  - unraid
  - cloudflare
  - fail2ban
---

When using a Cloudflare Tunnel, passing the source IP to the destination requires some additional configuration, as Cloudflare typically acts as a reverse proxy, which means it will terminate the client connection and create a new connection to your server. However, there are ways to forward the original client's IP address to your backend server.

Here's how you can achieve this:

### 1. Configure Cloudflare Tunnel
- First, ensure that your Cloudflare Tunnel is properly configured and running. You can follow the Cloudflare Tunnel setup guide provided by Cloudflare to get started.

### 2. Use cf-connecting-ip Header
- Cloudflare adds the original client's IP address to the cf-connecting-ip header. You need to configure your backend server to read this header to get the client's IP address.

### 3. Configure Your Web Server
- Depending on your web server, the configuration will vary. Here are examples for Nginx and Apache:
- You can use the real_ip module to set the client IP from the cf-connecting-ip header.
- Open your Nginx configuration file found at /etc/nginx/nginx.conf.
- Add the following lines to set the real IP:

```
http {
    ...
    set_real_ip_from 0.0.0.0/0;
    real_ip_header CF-Connecting-IP;
    ...
}
```
### 4. Reload Nginx to apply the changes:

```
/etc/rc.d/rc.nginx restart
/etc/rc.d/rc.nginx reload
```
