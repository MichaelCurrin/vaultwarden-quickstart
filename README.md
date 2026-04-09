# Vaultwarden quickstart
> Setup a free self-hosted alternative to Bitwarden

## Motivation and disclaimer

Self-hosting means more control over your data (where it is stored, and if hackers target Bitwarden then your data is not there, and also Vaultwarden is an open source solution so you're not locked into Bitwarden as an orgasation/provider).

But there are risks like your DB or server getting hacked or getting locked out, plus the cost and effort needed to setup and run this yourself and to patch with security updates.

Some discussions on Reddit say that the risks outweight the benefit, so just go for the paid or free tier for BitWarden.


## Links

- Repo: [dani-garcia/vaultwarden](https://github.com/dani-garcia/vaultwarden)
- Wiki: [Vaultwarden Wiki](https://github.com/dani-garcia/vaultwarden/wiki)


## Pre-requisites

- Docker or Docker alternative.

## Security

Note that Vaultwarden is setup by default to only be accessible with **HTTPS**. This requires using a reverse proxy (Nginx as setup here) and SSL certificate. See [Proxy examples](https://github.com/dani-garcia/vaultwarden/wiki/Proxy-examples) in the Wiki for alternatives and more info.

## Setup and run locally

This is good to test running Vaultwarden locally and access as a web app. Note the limitation is you cannot use a browser extension.

1. Generate certificate. Example of self-signed certificate generation:
    ```sh
    mkdir -p ssl
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
      -keyout ./ssl/key.pem \
      -out ./ssl/cert.pem \
      -subj "/C=US/ST=State/L=City/O=Organization/CN=localhost"
    ```
1. Start the containers.
    ```sh
    docker compose up
    ```
1. Open in the browser: https://127.0.0.1 OR https://localhost
    - Uses HTTPS but your browser will probably warn you that this certificate is not secure and you have to click to proceed anyway.

See [docker-compose.yaml](/docker-compose.yaml). This setup persists the Vaultwarden data in a volume on the host, in the repo, but you might want to put this is a global location.

## Deploy

If you want to access the app through browser extension:

1. Setup persistent server with strong security. This should include the Vaultwarden container and a reverse proxy (as a managed solution like API Gateway or as a container).
    - Cloud: This can be on AWS or Digital Ocean or Oracle free tier.
    - On premises: On your own computer. Run localhost and setup public IP with Cloudfare or Ngrok. This needs to be static.
1. Install Bitwarden extension in the browser.
1. Login to the extension. Pick self-hosted and enter your custom URL.

Once you have a user registered and want to prevent more registrations, set `SIGNUPS_ALLOWED` to be false the environment variables such as [docker-compose.yaml](/docker-compose.yaml).

### Notes

- For using Ngrok, setup Ngrok up as a service that starts when you machine reboots.
- Check what CPU and RAM requirements you need.
- Kubernetes is overkill - the RAM Kubernetes uses is about 7x what Vaultwarden uses.
- Make sure your data is **persisted** - `vw-data` will container SQLite DBs and you want to make sure these are not lost. Perhaps another DB backend can be used.
- The flow for setting up and configure certificates and configuring to deploy depends on what solution you choose and that is not covered here. Keep your certificates **secret**.
- If you want to be more secure than Bitwarden, make sure the Vaultwarden app is not available publically on the internet and rather on a private network.

## License

Licensed under [MIT](/LICENSE) by [MichaelCurrin](https://github.com/MichaelCurrin).
