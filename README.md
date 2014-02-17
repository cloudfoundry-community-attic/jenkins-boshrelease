# Bosh release for Jenkins

One of the fastest ways to get [Jenkins](http://jenkins-ci.org/) running on any infrastructure is too deploy this bosh release.

<img src="https://www.evernote.com/shard/s3/sh/7b72031f-d254-4d4a-b90c-56636020f25c/177fbe83e857be5fca8d306cfb4aa88a/deep/0/Dashboard%20%5BJenkins%5D.png" />

## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_URL
bosh login
git clone https://github.com/cloudfoundry-community/jenkins-boshrelease.git
cd jenkins-boshrelease
bosh upload release releases/jenkins-3.yml
```

You now need a deployment file. This is different for each use case.

### bosh-lite/warden deployments

Copy the `examples/try_me.yml` to say `~/deployments/bosh-lite-jenkins.yml`.

Then target it, update it with the bosh-lite/warden template, and deploy it:

```
bosh deployment ~/deployments/bosh-lite-jenkins.yml
bosh -n diff templates/v1/warden_deployment_file.yml.erb
bosh -n deploy
```

You can now view Jenkins in your browser at http://10.244.1.2/

### AWS or OpenStack deployments

There are three additional manual steps than above:

* acquire a public IP address, say `1.2.3.4`
* create a security group with ports 22 & 80 open, say `jenkins`

Create a simple initial deployment file, say `~/deployments/jenkins.yml`:

``` yaml
---
name: myjenkins
director_uuid: <%= `bosh status | grep UUID | awk '{print $2}'` %>
networks: {}
properties:
  jenkins:
    ip_address: 1.2.3.4
    security_group: jenkins
    persistent_disk: 4096
    password: jEnKins
```


Then target it, update it with the simple template, and deploy it:

```
bosh deployment ~/deployments/jenkins.yml
bosh -n diff templates/v1/simple_deployment_file.yml.erb
bosh -n deploy
```


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

