# BOSH NTP Release

BOSH release for the Network Time Protocol (NTP) Server

## How To

In this example, we deploy to BOSH Lite:

```
bosh -e vbox -d ntp deploy manifests/ntp-lite.yml
# query the IP address for time
ntpdate -q 10.244.0.34 # macOS, old linux
```

### [Optional] Comment-out `ntpdate` cronjob

Our stemcell had an `ntpdate` cronjob; we commented it out: `ntpdate` is a coarse timekeeper and not necessary when `ntpd` is running. We ssh'ed into our VM and did the following:

```
sudo crontab -e
    #0,15,30,45 * * * * /var/vcap/bosh/bin/ntpdate
```

## Developer Notes

Developer notes, such as building and test a release, are available [here](docs/DEVELOPER.md).
