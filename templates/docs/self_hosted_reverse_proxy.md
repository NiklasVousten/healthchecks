# Reverse Proxy

Running healthchecks behin a reverse proxy can result in a CSRF error.
While django has a `CSRF_TRUSTED_ORIGINS` option, which can resolve this issue, this is not needed with the correct config.
The following list some example configs for your reverse proxy as well as additional options which might be set in oder for django to understand these.

In general it is important to pass the host and scheme to healthchecks via the reverse porxy.

## Nginx

The following is a minimal example for nginx.
Some use `$http_host` instead of `$host`, but that can result in a security issue due to [host spoofing](https://github.com/yandex/gixy/blob/6f68624a7540ee51316651bda656894dc14c9a3e/docs/en/plugins/hostspoofing.md).

```text
location / {
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://localhost:8000;
}
```

To prevent django from still failing, the following line has to be added to `local_settings.py`

```python
SECURE_PROXY_SSL_HEADER = ("HTTP_X_FORWARDED_PROTO", "https")
```

## HAProxy

## Caddy