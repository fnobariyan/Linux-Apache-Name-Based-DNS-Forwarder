# Linux-Apache-Name-Based-DNS-Forwarder
This project demonstrates how to configure Name-Based Virtual Hosting in Apache together with a DNS Server and DNS Forwarder in a VirtualBox lab environment.

The goal is to host multiple websites on a single IP address and access them using different domain names.

## 🏗️ Lab Architecture
                         Windows Host
                       192.168.56.1
                              │
                              │ Host-Only Network
                              │
              ┌───────────────┴────────────────┐
              │                                │
              ▼                                ▼
       DNS Forwarder                      Apache Server
       192.168.56.103                    192.168.56.106
              │                                │
              ▼                                │
        DNS Server                             │
       192.168.56.102                          │
              │                                │
              └───────────────┐                │
                              ▼                ▼
                     mcloud.local DNS Records
                              │
                    ┌─────────┴─────────┐
                    │                   │
                    ▼                   ▼
             www.mcloud.local     dl.mcloud.local
                    │                   │
                    └─────────┬─────────┘
                              ▼
                       192.168.56.106
                              │
                         Apache HTTPD
                              │
                ┌─────────────┴─────────────┐
                │                           │
                ▼                           ▼
         www VirtualHost             dl VirtualHost


## 🖥️ Environment

         | Component     | Address / Configuration |
| ------------- | ----------------------- |
| Windows Host  | `192.168.56.1`          |
| DNS Server    | `192.168.56.102`        |
| DNS Forwarder | `192.168.56.103`        |
| Apache Server | `192.168.56.106`        |
| Network       | `192.168.56.0/24`       |
| Web Server    | Apache HTTPD            |
| OS            | Rocky Linux             |
| Web Port      | `80`                    |

## 🌐 DNS Configuration

The following DNS records are configured:

www.mcloud.local    → 192.168.56.106
dl.mcloud.local     → 192.168.56.106

Both domain names point to the same IP address.

This is important because the project uses Name-Based Virtual Hosting.

## 🔀 DNS Resolution Flow

When the Windows client requests:

www.mcloud.local

the request follows this path:

Windows Client
      │
      ▼
DNS Forwarder
192.168.56.103
      │
      ▼
DNS Server
192.168.56.102
      │
      ▼
192.168.56.106

## The same process is used for:

dl.mcloud.local

## 🌍 Apache Name-Based Virtual Hosting

Both websites are hosted on:

192.168.56.106:80

Apache distinguishes between them using the HTTP Host header.

www.mcloud.local
<VirtualHost 192.168.56.106:80>
    ServerName www.mcloud.local
    DocumentRoot /var/www/html/www-mcloud
</VirtualHost>

dl.mcloud.local
<VirtualHost 192.168.56.106:80>
    ServerName dl.mcloud.local
    DocumentRoot /var/www/html/dl-mcloud
</VirtualHost>

## 🔍 Testing DNS

DNS resolution was tested from Windows using:

nslookup www.mcloud.local

Expected result:

Name:    www.mcloud.local
Address: 192.168.56.106

And:

nslookup dl.mcloud.local

Expected result:

Name:    dl.mcloud.local
Address: 192.168.56.106

## 🧪 Final Test

After configuring the Windows client to use the DNS Forwarder:

DNS Server:
192.168.56.103

the following URLs can be accessed directly from the browser:

http://www.mcloud.local
http://dl.mcloud.local

Each domain displays its own website while both domains use the same server IP.
