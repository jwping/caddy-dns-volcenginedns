VolcEngine DNS module for Caddy
===========================

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with VolcEngine (火山引擎) accounts.

## Caddy module name

```
dns.providers.volcenginedns
```

## Config examples

To use this module for the ACME DNS challenge, [configure the ACME issuer in your Caddy JSON](https://caddyserver.com/docs/json/apps/tls/automation/policies/issuer/acme/) like so:

```json
{
  "module": "acme",
  "challenges": {
    "dns": {
      "provider": {
        "name": "volcenginedns",
        "access_key_id":"YOUR_VOLCENGINE_ACCESS_KEY_ID",
        "access_key_secret":"YOUR_VOLCENGINE_ACCESS_KEY_SECRET"
      }
    }
  }
}
```

or with the Caddyfile:

```
# globally

acme_dns volcenginedns {
  access_key_id {env.VOLCENGINE_ACCESS_KEY_ID}
  access_key_secret {env.VOLCENGINE_ACCESS_KEY_SECRET}
}
```

```
# one site

tls {
  dns volcenginedns {
    access_key_id {env.VOLCENGINE_ACCESS_KEY_ID}
    access_key_secret {env.VOLCENGINE_ACCESS_KEY_SECRET}
  }
}
```

You can replace `{env.VOLCENGINE_ACCESS_KEY_ID}` and `{env.VOLCENGINE_ACCESS_KEY_SECRET}` with the actual auth token in the `""` if you prefer to put it directly in your config instead of an environment variable.

## Authenticating

See [the associated README in the libdns package](https://github.com/libdns/volcenginedns) for important information about credentials.
