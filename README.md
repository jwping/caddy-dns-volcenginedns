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

## DNS Propagation Delay

**Important**: VolcEngine DNS records may take approximately 3-6 minutes to propagate after being added. Without configuring a propagation delay, you may encounter DNS validation errors such as:

```json
{
  "identifier": "*.example.com",
  "challenge_type": "dns-01",
  "problem": {
    "type": "urn:ietf:params:acme:error:dns",
    "title": "",
    "detail": "DNS problem: NXDOMAIN looking up TXT for _acme-challenge.example.com - check that a DNS record exists for this domain"
  }
}
```

To avoid this issue, configure `propagation_delay` and `propagation_timeout` in your TLS configuration:

```
{
    email admin@example.com
    dns volcenginedns {
        access_key_id "YOUR_VOLCENGINE_ACCESS_KEY_ID"
        access_key_secret "YOUR_VOLCENGINE_ACCESS_KEY_SECRET"
    }
}

# Wildcard domain configuration
*.example.com {
    tls {
        # Wait before checking DNS TXT record (match your TTL, typically 10 minutes)
        propagation_delay 10m
        # Maximum wait timeout (slightly longer than delay to avoid premature termination)
        propagation_timeout 15m
        # Optional: specify public DNS resolvers to avoid local cache interference
        resolvers 223.5.5.5 119.29.29.29
    }

    # Your site configuration here
    handle {
        respond "Hello, World!"
    }
}
```

**Configuration notes**:
- `propagation_delay`: The time to wait before checking if the DNS TXT record exists. Set this to match your DNS TTL (typically 10 minutes for VolcEngine).
- `propagation_timeout`: The maximum time to wait for DNS propagation. Should be slightly longer than `propagation_delay` to avoid premature termination.
- `resolvers`: Optional. Specify public DNS resolvers (e.g., `223.5.5.5`, `119.29.29.29`) to avoid local DNS cache interference during validation.

## Authenticating

See [the associated README in the libdns package](https://github.com/jwping/libdns-volcenginedns) for important information about credentials.
