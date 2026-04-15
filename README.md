# Vaultwarden quickstart
> Setup a free self-hosted alternative to Bitwarden


This repo is can be up and running locally in a few commands so this is an easy experiment, but if you want to host it on the cloud so you can integrate with the Bitwarden extension, that is a lot more involved. See [Notes](#notes) for more info.


## Links

- Repo: [dani-garcia/vaultwarden](https://github.com/dani-garcia/vaultwarden)
- Wiki: [Vaultwarden Wiki](https://github.com/dani-garcia/vaultwarden/wiki)


## Prerequisites

- Docker or Docker alternative.
- OpenSSL - install as per [guide](https://michaelcurrin.github.io/dev-cheatsheets/cheatsheets/shell/commands/openssl.html).

## Local offline soltion

### Setup and run locally

This is good to test running Vaultwarden locally and access as a web app. Note the limitation is you cannot use a browser extension.

1. Generate certificate. Example of self-signed certificate generation:
    ```sh
    mkdir -p ssl
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
      -keyout ./ssl/key.pem \
      -out ./ssl/cert.pem \
      -subj "/C=US/ST=State/L=City/O=Organization/CN=localhost"
    ```
1. Start the containers:
    ```sh
    docker compose up
    ```
1. Open in the browser at https://127.0.0.1 OR https://localhost
    - This use HTTPS but your browser will probably warn you that this certificate is not secure then you have to click to proceed anyway.

See [docker-compose.yaml](/docker-compose.yaml). This setup persists the Vaultwarden data in a volume on the host, in the repo, but you might want to put this is a global location.

### Use this approach

If you want to use Vaultwarden hosted locally and are happy to manually save and fill results in the browser, you can keep doing that with this solution without deploying to the cloud. Just make sure to backup your data and don't lose your password.

If you want to use the Bitwarden **browser extension** with Vaultwarden, follow the next section.

## Public IP solution

### Integration with Bitwarden extension

If you want to access the app through browser extension:

1. Setup persistent server with strong security. This should include the Vaultwarden container and a reverse proxy (as a managed solution like API Gateway or as a container).
    - Cloud: This could be on AWS or Digital Ocean or Oracle free tier. This requires possible costs, maintainance, and public internet exposure.
    - On-premise: On your own computer. Run localhost and setup secure tunnel with public IP with Cloudfare or Ngrok. This needs to be a **static** URL. Safest to setup IP restriction or basic auth.
1. Install Bitwarden extension in the browser - [Dowload](https://bitwarden.com/download/).
1. Login to the extension. Pick self-hosted and enter your custom URL.

Once you have a user registered and want to prevent more registrations, set `SIGNUPS_ALLOWED` to be false the environment variables such as [docker-compose.yaml](/docker-compose.yaml).

### Notes on this approach

- For using Ngrok, setup Ngrok up as a service that starts when you machine reboots.
- For cloud solution, check what CPU and RAM requirements you need.
- Kubernetes is overkill - the RAM Kubernetes uses is about 7x what Vaultwarden uses.
- Make sure your data is **persisted** - `vw-data` will container SQLite DBs and you want to make sure these are not lost. Perhaps another DB backend can be used.
- The flow for setting up and configure certificates and configuring to deploy depends on what solution you choose and that is not covered here. Keep your certificates **secret**.
- If you want to be more secure than Bitwarden, make sure the Vaultwarden cloud-hosted app is not available publicly on the internet and rather on a private network.

## Notes

General notes about the project

### Motivation and disclaimer

Self-hosting means more **control** over your data (where it is stored, and if hackers target Bitwarden then your data is not there, and also Vaultwarden is an open source solution so you're not locked into Bitwarden as an orgasation/provider).

But there are **risks** to this approach, like your DB or server gets hacked or getting locked out, plus the cost and effort needed to setup and run this yourself and to patch with security updates.

Some discussions on Reddit say that the risks outweight the benefit and that it is only more secure then Bitwarden if your solution is not on the public internet. So then you can just accept the paid or free tier from BitWarden.

### Security note

Note that Vaultwarden is setup by default to only be accessible with **HTTPS**. This requires using a reverse proxy (Nginx as setup here) and SSL certificate. See [Proxy examples](https://github.com/dani-garcia/vaultwarden/wiki/Proxy-examples) in the Wiki for alternatives and more info.

### Operating system

This guide is written for macOS / Linux and you have to adapt it if you want to run on Windows. Specifically, generating a certificate.


## License

Licensed under [MIT](/LICENSE) by [MichaelCurrin](https://github.com/MichaelCurrin).
