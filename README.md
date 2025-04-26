# Bluesky PDS Easy Deployment

This repository provides a step-by-step guide for deploying the Bluesky Personal Data Server (PDS) on a Linux system.
It is similar to [bluesky-social/pds](https://github.com/bluesky-social/pds), but uses only Docker to run the services.
Unlike the `installer.sh` script in the [bluesky-social/pds](https://github.com/bluesky-social/pds) repository, this setup relies solely on the commands provided below, making the deployment process simpler and more straightforward.

## Prerequisites

- A server (minimum 1 CPU core, 1 GB RAM) with a public IP address and firewall rules allowing traffic on ports 80 and 443
- OS (Ubuntu 20.04/22.04 or Debian 11/12 preferred)
- Docker
- Docker Compose
- A domain name

## Installation

### 1. Set up DNS records

Create DNS records to point your domain name to your server's IP address.

| Type | Name                | IP      | TTL |
| ---- | ------------------- | ------- | --- |
| A    | bsky.example.com    | 1.2.3.4 | 600 |
| A    | \*.bsky.example.com | 1.2.3.4 | 600 |

### 2. Clone the repository

```sh
git clone https://github.com/aykhans/bsky-pds-easy.git && cd bsky-pds-easy
```

### 3. Generate the `pds.env` file

```sh
cp pds.env.example pds.env
```

### 4. Configure the `pds.env` file

Generate `PDS_JWT_SECRET` with OpenSSL:

```sh
sed -i "s/PDS_JWT_SECRET=/PDS_JWT_SECRET=$(openssl rand --hex 16)/" pds.env
```

Generate `PDS_ADMIN_PASSWORD` with OpenSSL:

```sh
sed -i "s/PDS_ADMIN_PASSWORD=/PDS_ADMIN_PASSWORD=$(openssl rand --hex 16)/" pds.env
```

Generate `PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX` with OpenSSL:

```sh
sed -i "s/PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX=/PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX=$(openssl ecparam --name secp256k1 --genkey --noout --outform DER | tail --bytes=+8 | head --bytes=32 | xxd --plain --cols 32)/" pds.env
```

Then, manually fill in the following fields in your `pds.env` file:

```sh
PDS_HOSTNAME=bsky.example.com
PDS_EMAIL_SMTP_URL=smtps://username:password@smtp.emailserver.com/
PDS_EMAIL_FROM_ADDRESS=from@example.com
```

### 5. Start the services with Docker Compose

```sh
docker compose up -d
```

## Next Steps

After successfully starting the services, you can create an account using [bsky-pdsadmin-dockerized](https://github.com/aykhans/bsky-pdsadmin-dockerized).

Example:

```sh
docker run -it --rm --env-file ./pds.env aykhans/bsky-pdsadmin account create
```

You can now visit [https://bsky.app](https://bsky.app) and log in using your custom PDS server (`bsky.example.com`) and your newly created credentials.
