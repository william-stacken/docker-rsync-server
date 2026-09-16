# docker-rsync-server
Rsync server over SSH running in docker.

## Configuration
The tunnel is configured through the following environment variables:

- `RSYNC_USER`: The user that can log in and use rsync.
- `RSYNC_FLAGS`: The CLI flags to pass on to `rrsync`. See [here](https://www.man7.org/linux/man-pages/man1/rrsync.1.html)
                 for details.
- `RSYNC_PUBLIC_KEY`: The public key for the rsync user.
- `RSYNC_DIR`: The directory to use as the rsync upload root.

## Usage
Docker compose example:
```yml
services:
  rsync:
    image: ghcr.io/william-stacken/rsync-server:latest
    environment:
      RSYNC_USER: rsync
      RSYNC_PUBLIC_KEY: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIDO2tvIOSuhNvK7fkE7gAkUXPbFa5hRujmnH0G1Wdy0 only-used-for-testing
      RSYNC_FLAGS: -wo
      RSYNC_DIR: /data/my-rsync-dir
    volumes:
      - my-shared-data:/data
      - my-ssh-config:/etc/ssh
    ports:
      - "127.0.0.1:2222:22"
```
The client can upload files (if permitted by the `$RSYNC_FLAGS`) using this command:
```sh
rsync -avz mydirectory/ $RSYNC_USER@$RSYNC_HOST: 
```
This uploads the files under `./mydirectory/` on the client to the `$RSYNC_DIR`
directory on the server.

To download files from `$RSYNC_DIR` (if permitted by the `$RSYNC_FLAGS`) use this command:
```sh
rsync -avz $RSYNC_USER@$RSYNC_HOST: mydirectory/
```

## Demo

Start by running `cd test && ./setup && docker compose up -d`. Then, run this command:
```sh
rsync -avze "ssh -i keys/id_ed25519 -o IdentitiesOnly=yes -p 2222" keys/ rsyncuser@localhost:
```
Now open http://localhost:8080. You should see that your generated SSH keys was uploaded and
are accessible on the web server under the `rsync` directory.

## Publishing
`GITHUB_ACTOR=$YOUR_NAME TAG=1.0 GITHUB_TOKEN=$YOUR_TOKEN ./publish`
