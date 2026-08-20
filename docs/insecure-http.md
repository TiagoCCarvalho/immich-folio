# LAN-only HTTP deployments

Immich Folio marks its session cookies with the `Secure` attribute in
production builds. Browsers silently **drop** Secure cookies over plain
`http://` (localhost excepted). On a deployment reached via
`http://<nas-ip>:<port>` this makes the admin panel appear broken in a
confusing way:

- login succeeds, but the session is "lost" on every navigation or reload
- the album picker shows "No albums found"
- the page builder shows an empty gallery even though `gallery.yaml` is
  populated
- every `/api/admin/*` request returns 401 with no visible error

The same applies to password-protected albums/subpages: visitors over HTTP
can never keep the auth cookie.

## Fix

Preferred: serve Folio over HTTPS (reverse proxy with a certificate, or
Tailscale Serve/Funnel).

For a trusted-LAN HTTP deployment, set:

```env
ALLOW_INSECURE_COOKIES=true
```

This removes the `Secure` attribute from the admin session cookie and the
album/subpage auth cookies. Never set it on an instance reachable over the
public internet via HTTP.
