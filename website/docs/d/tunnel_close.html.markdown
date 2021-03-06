---
layout: "ssh"
page_title: "SSH: ssh_tunnel_close"
sidebar_current: "docs-ssh-data-source-tunnel-close"
description: |-
  Closes SSH tunnel local connections.
---

# ssh_tunnel

This data source is a workaround which should depend on a last resource/module, which requires SSH tunnel.

## Example Usage

```hcl
provider "ssh" {}

data "ssh_tunnel" "consul" {
  user = "root"
  auth {
    private_key {
      content = file(pathexpand("~/.ssh/id_rsa"))
    }
  }
  server {
    host = "localhost"
    port = 22
  }
  remote {
    port = 8500
  }
}

provider "consul" {
  address = data.ssh_tunnel.consul.local.0.address
  scheme  = "http"
}

data "consul_keys" "keys" {
  key {
    name = "revision"
    path = "secrets/api/password"
  }
}

data "ssh_tunnel_close" "consul" {
  depends_on = [data.consul_keys.keys]
}
```