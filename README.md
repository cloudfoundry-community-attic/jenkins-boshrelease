# Bosh release for Jenkins

One of the fastest ways to get [Jenkins](http://jenkins-ci.org/) running on any infrastructure is too deploy this bosh release.

## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_URL
bosh login
git clone git@github.com:cloudfoundry-community/jenkins-boshrelease.git
cd jenkins-boshrelease
bosh upload release releases/jenkins-1.yml
```

Now edit the `examples/aws.yml` or `examples/openstack*.yml` with your bosh' UUID (run `bosh status` to get it).

Finally, target and deploy. For deployment to a bosh running on aws:

```
bosh deployment examples/aws.yml
bosh deploy
```

If you deploy more than one instance in a job it assumes that you want replication, and you need to specify the IP address of the master node in your deployment manifest.

## Development


## Create new final release

To create a new final release you need to get read/write API credentials to the [@cloudfoundry-community](https://github.com/cloudfoundry-community) s3 account.

Please email [Dr Nic Williams](mailto:&#x64;&#x72;&#x6E;&#x69;&#x63;&#x77;&#x69;&#x6C;&#x6C;&#x69;&#x61;&#x6D;&#x73;&#x40;&#x67;&#x6D;&#x61;&#x69;&#x6C;&#x2E;&#x63;&#x6F;&#x6D;) and he will create unique API credentials for you.

Create a `config/private.yml` file with the following contents:

``` yaml
---
blobstore:
  s3:
    access_key_id:     ACCESS
    secret_access_key: PRIVATE
```

You can now create final releases for everyone to enjoy!

```
bosh create release
# test this dev release
git commit -m "updated jenkins"
bosh create release --final
git commit -m "creating vXYZ release"
git tag vXYZ
git push origin master --tags
```

