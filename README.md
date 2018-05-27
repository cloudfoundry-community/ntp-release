# BOSH NTP Release

BOSH release for the Network Time Protocol (NTP) Server

## How To

In this example, we deploy to BOSH Lite:

```
bosh -e vbox -d ntp deploy manifests/ntp-lite.yml
# query the IP address for time
ntpdate -q 10.244.0.34 # macOS, old linux
```

### Use [`post-start`](https://bosh.io/docs/post-start/) To Disable Competing Timekeepers

You don't need to make changes to your manifest if you're deploying to Ubuntu
Trusty stemcell — our default `post_start` property disables the cron job which
runs every fifteen minutes to synchronize time.

If you're using the Ubuntu Xenial stemcell, you many want to set the
`post_start` property to disable the `chrony` service by adding something similar
to the following to the `ntpd` job properties:

```yaml
post_start: |
  #!/bin/bash -x
  # on Xenial we disable chrony because we're running ntpd
  systemctl disable chrony.service
  systemctl stop    chrony.service
```

Note: It's not terribly important to disable competing timekeepers; all
timekeepers, if properly configured, should all converge to the same time.

## Developer Notes

Developer notes, such as building and test a release, are available [here](docs/DEVELOPER.md).
