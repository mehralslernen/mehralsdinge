# FaSTTUBe Corona Tracker

Small webapp to track who was in which room at which time to backtrace
potential viral infections.

For Ideas, Progress, and Bugs visit
[Issues](https://git.fasttube.de/FaSTTUBe/ftracker/issues).

## Requirements

- Unixoid system (linux, BSD, macOS). Windows might also work.
- `python` 3.6+ (might be `python3` on your system)
- `pip` for python 3+ (might be `pip3` on your system)

## How to run

(Dev setup, for prod deployment see below)

```bash
# clone, cd into repo
pip install -e .
python3 -m ftracker
```

Edit `config.ini` to tune your installation.

Then, point your browser at <http://localhost:5000/>.

## Installation/Deployment

There are 2 methods: Docker and Manual.

### Method A: Docker

If you want to manage SSL in your own webserver, you can simply run

```bash
sudo docker run \
	-d \
	--name ftracker \
	-p 80:80 \
	-v /your/full/path/to/config.ini:/etc/ftracker/config.ini \
	fasttube/ftracker
```

If you want the container to also handle SSL so it can run standalone you need
to build it like this:

```bash
# clone, cd into repo
sudo docker build . -t ftracker --build-arg DOMAIN=ftracker.example.com
```

And then run it:

```bash
sudo docker run \
	-d \
	--name ftracker \
	-p 80:80 \
	-v /your/full/path/to/config.ini:/etc/ftracker/config.ini \
	ftracker
```

To stop/start the container afterwards, run:

```bash
docker stop ftracker # might take up to 10 seconds
docker start ftracker
```

### Method B: Manual

#### 1. FTracker Backend

Install backend system wide:
```bash
# clone, cd into repo
sudo -H pip install . # Use -e if you want to hack on the backend while installed.
```

#### 2. WSGI Server + Service file

You need a WSGI Middleware (using `Flask`'s included `werkzeug` is discouraged
for production environments). I recommend `uwsgi` since it's flexible, fast and
has `nginx` integration. A sample configuration file as well as service
description files for both `systemd` and `rc` are included in `res/` for you to
adapt (file paths etc.) and install to your system (The `systemd` service file
still untested though, feel free to leave feedback).

#### 3. Webserver

You need a webserver. I recommend `nginx` because it's the industry standard
and fast. A sample config file is included in `res/` for you to adapt (domain,
SSL certs) and install to your system. The configuration should include:
Webroot in `web/` with a fallback to the WSGI handler for the backend.

Enabling SSL (https) and redirecting http to https is strongly encouraged, i
recommend using `Let's Encrypt`'s `certbot` to easily obtain certificates.

#### 4. Customization

Edit `config.ini` to your liking. Restart the backend by restarting the `uwsgi`
service, e.g. `sudo systemctl restart ftracker` or `sudo service ftracker
restart`

## Open Sources

This project uses the `QRCode.js` library (Copyright (C) 2012 davidshimjs)
licensed under the MIT License, see `web/qrcodejs/LICENSE`. Thanks!

## License

FTracker is licensed under the GNU GPL v3 license, see
[LICENSE.md](https://git.fasttube.de/FaSTTUBe/ft-corona-tracker/src/branch/master/LICENSE.md)
for details.

Copyright (C) 2020 Oskar/FaSTTUBe
