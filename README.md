---
layout: "docs"
page_title: "Drivers: pot-task-driver"
sidebar_current: "docs-drivers-community-pot-task-driver"
description: |-
  The Pot task driver is used to run application containers using FreeBSD pot.
---

# Pot task Driver

Name: `pot-task-driver`

The Pot task driver provides an interface for using [pot][pot-github-repo] for dynamically running applications inside a FreeBSD Jail. 
You can download the external pot-task-driver [here][pot-task-driver]. For more detailed instructions on how to set up and use this driver, please refer to the [guide][pot-task-guide].

## Task Configuration

```hcl
task "nginx-pot" {
    driver = "pot"

    config {
      image = "https://pot-registry.zapto.org/registry/"
      pot = "FBSD120-nginx"
      tag = "1.0"
      command = "nginx -g 'daemon off;'"
      network_mode = "public-bridge" 
      port_map = {
        http = "80"
      }
      copy = [
        "/root/index.html:/usr/local/www/nginx-dist/index.html",
        "/root/nginx.conf:/usr/local/etc/nginx/nginx.conf"
      ]
      mount = [ 
        "/tmp/test:/root/test",  
      ]
      mount_read_only = [
        "/tmp/test2:/root/test2"
      ]
   }
}
```

The pot task driver supports the following parameters: 

* `image` - The url for the http registry from where to get the image.

* `pot` - Name of the image in the registry.

* `tag` - Version of the image.
 
* `commad` - Command that is going to be executed once the jail is started.

* `network_mode` - (Optional) Defines the network mode of the pot. Default: **"public-bridge"**
 Possible values are:
  **"public-bridge"**  pot creates an internal virtual network with a NAT table where all traffic is going to be sent.
  **"host"** pot bounds the jail directly to a host port.

* `port_map` - (Optional) Sets the port on which the application is listening inside of the jail. If not set, the application will inherit the port configuration from the image.

* `copy` - (Optional) Copies a file from the host machine to the pot jail in the given directory.

* `mount` - (Optional) Mounts a read/write folder from the host machine to the pot jail.

* `mount_read_only` - (Optional) Mounts a read only directory inside the pot jail.

## Client Requirements

`pot-task-driver` requires the following:

* 64-bit FreeBSD 12.0-RELEASE host .
* The FreeBSD's Nomad binary (available as a package).
* The pot-task-driver binary placed in the [plugin_dir][plugin_dir] directory. 
* Installing [pot][pot-github-repo] and following the install [guide][pot-install-guide].
* Webserver from where to serve the images. (simple file server)


[pot-task-driver]: https://github.com/trivago/pot-task-driver
[plugin_dir]: /docs/configuration/index.html#plugin_dir
[pot-github-repo]: https://github.com/pizzamig/pot
[pot-install-guide]: https://github.com/pizzamig/pot/blob/master/share/doc/pot/Installation.md
