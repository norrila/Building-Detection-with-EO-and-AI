# Data accounts and API keys

The pipeline downloads satellite data from services that need an account. This page says how to get the
credential each one needs, and how to keep it safe. Each step is taken from the service's own
documentation, which is linked and dated. If a service has changed its pages since, follow theirs.

**A key is a password.** Never paste it into a chat, an e-mail, a script or a file that is committed to
git. The pipeline reads credentials from the operating system's keychain (Windows Credential Manager,
macOS Keychain or the Linux Secret Service), and stores them through a prompt that does not echo what you
type, so the key does not end up in your shell history either.

## Planet (PlanetScope imagery) — API key

PlanetScope is commercial imagery. Searching the catalogue is free once you have an account, but
**ordering imagery uses your account's quota or credits**. What an account may order depends on its
subscription, which is agreed with Planet and is not covered here.

1. **Get an account.** Sign up or sign in at <https://www.planet.com/account/>. Organisations usually get
   their accounts through a Planet contract; ask your organisation's Planet administrator first.
2. **Find the key.** Planet: "Your API key can be found on your Account page under the My Settings tab."
   Copy it from there.
3. **There is only one.** Planet: "You may only have one active API key at a time." Everyone who uses
   the same account uses the same key, so give each person their own account where you can.
4. **If the key is exposed** (pasted somewhere public, committed to git, sent by e-mail), Planet's
   instruction is: "If your API key is exposed, contact Planet Technical Support."
5. **Store it for the pipeline:**

       python credentials.py set planet

   It asks for the key without showing it. `python credentials.py check` shows what is stored (the
   length of each secret, never the secret itself).

How the key is used: Planet accepts it as HTTP Basic authentication, the key as the user name and an empty
password, or as the header `Authorization: api-key <key>`. It gives the same access as the account
owner, which is why it is kept like a password.

**API key or OAuth client?** Planet also issues OAuth2 client credentials (a client ID and a secret, from
the *OAuth Clients* panel of the account). They are a different credential, for the Planet Insights
Platform (formerly Sentinel Hub, `services.sentinel-hub.com`). For the Data and Orders APIs on
`api.planet.com`, which this pipeline uses, Planet states that "API key authentication is recommended".
Planet also says it intends to replace API keys with OAuth2 eventually, with no date set. If you have an
Insights client, store it with `python credentials.py set planet-insights`; a subscription bought on
Insights does not carry over to the classic API key.

Source: Planet, "Authentication", <https://docs.planet.com/develop/authentication/>, last updated
8 June 2026, read 19 October 2026.

## Copernicus Data Space Ecosystem (Sentinel-2) — account

The Copernicus Sentinel data are free, but downloading them from ESA's own archive needs a free account.
The pipeline uses it for Sentinel-2 at the current processing baseline, including the reprocessed 2018
archive.

1. **Register** at <https://dataspace.copernicus.eu> (Register).
2. **Leave two-factor authentication off for this account, or use a separate account for the pipeline.**
   With two-factor authentication on, every login needs a one-time code, which a download that runs
   unattended cannot give.
3. **Store it for the pipeline:**

       python credentials.py set cdse

   It asks for the user name (your e-mail address) and the password. The pipeline exchanges them for an
   access token, which lasts 10 minutes, and renews it as it goes.

A free account may keep 4 downloads open at once and transfer 12 TB a month.

Sources: Copernicus Data Space Ecosystem documentation, "Access token",
<https://documentation.dataspace.copernicus.eu/APIs/Token.html>, and "Quotas and Limitations",
<https://documentation.dataspace.copernicus.eu/Quotas.html>, both read 19 October 2026.

## NASA Earthdata (Sentinel-1, NISAR) — account or token

Sentinel-1 and NISAR come from the Alaska Satellite Facility (ASF), which uses a NASA Earthdata Login.
Register at <https://urs.earthdata.nasa.gov/users/new>. A user token is preferred over the password: it
can be revoked on its own without changing the password, and it expires.

1. Sign in at <https://urs.earthdata.nasa.gov>, open your profile and use **Generate Token**.
2. NASA: "User tokens are valid for a duration of 60 days", so generate a new one when it runs out.
3. NASA: "A user is allowed a maximum of two valid user tokens at any given time"; delete one (the X
   button) before making a third.
4. Store it with `python credentials.py set earthdata` (or press Enter at the token prompt to store a
   user name and password instead).

Source: NASA Earthdata Login, "User Token", <https://urs.earthdata.nasa.gov/documentation/for_users/user_token>,
read 19 October 2026.
