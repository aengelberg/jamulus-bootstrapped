# Jamulus Bootstrapped

Some pre-tested configuration and easy-to-follow instructions for spinning up and managing a personal [Jamulus](https://jamulus.io/) Server.

This guide doesn't require much technical knowledge, but some familiarity with Linux command-line tools might help if you need to troubleshoot after-the-fact.

## [DigitalOcean](https://www.digitalocean.com/)

**Possible locations:** New York City, San Francisco, Toronto, and [other global locations](https://docs.digitalocean.com/products/platform/availability-matrix/)

**Price:** $5+/month

### Steps

Create a DigitalOcean Account, and go to the cloud portal.

Create a new Droplet:

<img width=50% src="https://user-images.githubusercontent.com/4122172/114340901-6e291b80-9b0d-11eb-9056-5feddf341e10.png"/>

To choose the droplet's image, go to "Marketplace", search for "Docker", and click on the Docker image. The selected image should look like this, though the version of Docker and Ubuntu doesn't really matter.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114341288-425a6580-9b0e-11eb-8338-08f1c2a6a54b.png"/>

Select the cheapest tier of compute: $5/month for 1GB of RAM, 1 CPU, and 25GB disk.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114341555-eba15b80-9b0e-11eb-81a4-e5057061c6dc.png"/>

Select whichever datacenter region is closest to you and whoever you plan to jam with.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114341661-25726200-9b0f-11eb-9370-120e78168411.png"/>

In the section for additional options, check the "User data" box and the "Monitoring" box:

<img width=50% src="https://user-images.githubusercontent.com/4122172/114342064-fb6d6f80-9b0f-11eb-9dc3-7dc90aedd468.png"/>

A new text box for "user data" will appear. Here we'll tell the Droplet to start Jamulus after it boots up. Copy the following config into that text box.

```yaml
#cloud-config
runcmd:
  - docker run --restart always --name jamulus -d -p 22124:22124/udp -v $(pwd)/jam:/jam grundic/jamulus
               -n -s -p 22124 -l /jam/jamulus.log -w "Welcome to Jamulus server."
```

This will download and run Jamulus in a Docker container, using a third-party [Jamulus Docker image](https://hub.docker.com/r/grundic/jamulus).
The config blob follows the [cloud-init](https://cloudinit.readthedocs.io/en/latest/) syntax.

The second line of flags are Jamulus's [Command Line Options](https://jamulus.io/wiki/Command-Line-Options), which you can configure how you'd like.

(TODO: explain how to set up recording via a docker volume)

In the Authentication section, upload your SSH key. Or if you aren't comfortable using SSH, create a new root password and hold onto it. These are only useful if you want to log into the machine and run commands while it's online.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114343406-a7b05580-9b12-11eb-8c2a-eba2751f400b.png"/>

(Optional) Enable the "backups" feature for an extra $1/month. This is only useful if you want to back up any saved recordings.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114343613-168dae80-9b13-11eb-9a12-96213720c953.png"/>

Finally, create your Droplet.

<img width=50% src="https://user-images.githubusercontent.com/4122172/114343705-52287880-9b13-11eb-9386-13b34ab76978.png"/>

This should take a minute or so to start up. Find the new Droplet's IP address, and connect to it from your Jamulus client:

<img width=50% src="https://user-images.githubusercontent.com/4122172/114343941-c8c57600-9b13-11eb-8a37-63c169cf6b51.png"/>

### Debugging

If Jamulus isn't automatically started up, log into the machine and confirm that the container is running via `docker ps`. If it isn't, you can try again by manually re-entering the `docker run` command from above.

You can also debug the startup script (to see if it successfully started up Jamulus) with `cat /var/log/cloud-init-output.log`. If it was successful you'd see something like:
```
Unable to find image 'grundic/jamulus:latest' locally
latest: Pulling from grundic/jamulus
e95f33c60a64: Pulling fs layer
e10f3b93c3bf: Pulling fs layer
f03ba9a3a39b: Pulling fs layer
e95f33c60a64: Verifying Checksum
e95f33c60a64: Download complete
f03ba9a3a39b: Verifying Checksum
f03ba9a3a39b: Download complete
e10f3b93c3bf: Verifying Checksum
e10f3b93c3bf: Download complete
e95f33c60a64: Pull complete
e10f3b93c3bf: Pull complete
f03ba9a3a39b: Pull complete
Digest: sha256:7bbec11680263de1f66396499f0a77effa889fabe5f0e7de08b5fecd03803758
Status: Downloaded newer image for grundic/jamulus:latest
fc9a59db4871302d094c8a050a89c9d5642f3c19b96459d8c83f5147242a6b36
```

DigitalOcean provides a "Rebuild Droplet" command in the "Destroy" menu, which will reset it to original settings and re-execute the `#cloud-config` script you originally provided.
