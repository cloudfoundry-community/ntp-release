## Updating Releases

```bash
export OLD_VERSION=4.2.8p12
export VERSION=4.2.8p13
cd ~/workspace/ntp-release
git pull -r
find packages/ntp -type f -print0 |
  xargs -0 perl -pi -e \
  "s/ntp-${OLD_VERSION}/ntp-${VERSION}/g"
 # FIXME: update README.md's download URL
bosh add-blob \
  ~/Downloads/ntp-${VERSION}.tar.gz \
  ntp/ntp-${VERSION}.tar.gz
vim config/blobs.yml
  # delete `ntp/ntp-${OLD_VERSION}.tar.gz` stanza
bosh create-release --force
bosh -e vbox upload-release
bosh -e vbox -n -d ntp \
  deploy manifests/ntp-lite.yml --recreate
 # `bosh -e vbox vms`; browse to ntp VM
# test
bosh -e vbox -d ntp ssh --command="/var/vcap/packages/ntp/bin/ntpq -c lpeers"
  # should look like the following (after stripping prefixes):
  # remote           refid      st t when poll reach   delay   offset  jitter
  # ==============================================================================
  #  time1.google.co .GOOG.           1 u   25   64    7   80.235   11.321   1.272
  #  time2.google.co .GOOG.           1 u   25   64    7   62.196    4.974   2.534
  #  time3.google.co .GOOG.           1 u   22   64    7  158.801    9.494   0.329
bosh upload-blobs
bosh create-release \
  --final \
  --tarball ~/Downloads/ntp-release-${VERSION}.tgz \
  --version ${VERSION} --force
git add -N releases/
git add -p
git ci -v
git tag $VERSION
git push
git push --tags
```
