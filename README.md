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

A "User Data" text box will appear. Here we'll tell the Droplet to start Jamulus after it boots up. Copy the following config into that text box.

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

In the Authentication section, upload your SSH key. Or if you aren't familiar with SSH, 

<img width=50% src="https://user-images.githubusercontent.com/4122172/114343406-a7b05580-9b12-11eb-8c2a-eba2751f400b.png"/>
