# docker-rsync-server
Rsync server over SSH running in docker.

The server uses the `/data` directory in the container as a root for all
files that are uploaded to it. By mounting a volume in this location, the
files can be persisted.

## Configuration
The tunnel is configured through the following environment variables:

- `RSYNC_USER`: The user that can log in and use rsync.
- `RSYNC_FLAGS`: The CLI flags to pass on to `rrsync`. See [here](https://www.man7.org/linux/man-pages/man1/rrsync.1.html)
                 for details.
- `RSYNC_PUBLIC_KEY`: The public key for the rsync user.

## Usage
The client can upload files (if permitted by the `$RSYNC_FLAGS`) using this command:
```sh
rsync -avz mydirectory/ $RSYNC_USER@$RSYNC_HOST: 
```
This uploads the files under `./mydirectory/` on the client to the `/data`
directory on the server.

To download files (if permitted by the `$RSYNC_FLAGS`) use this command:
```sh
rsync -avz $RSYNC_USER@$RSYNC_HOST: mydirectory/
```

## Demo

Start by running `cd test && ./setup && docker compose up -d`. Then, run this command:
```sh
rsync -avze "ssh -i keys/id_ed25519 -o IdentitiesOnly=yes -p 2222" keys/ rsyncuser@localhost:
```
Now open http://localhost:8080. You should see that your generated SSH keys was uploaded and
are accessible on the web server.

## Publishing
`GITHUB_ACTOR=$YOUR_NAME TAG=1.0 GITHUB_TOKEN=$YOUR_TOKEN ./publish`
