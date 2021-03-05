---
title: Cloud-native style intrusion and abnormality detection with Falco
date: '2020-01-01'
thumb_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/7uD6KyQOVCMIR9QdiTaW1V/4ff9790136b683ae320746365ea25381/ca6e5d1fd38951271f515ede1b7caf99201f0526-1460x820.png
thumb_img_alt: falco-camera
content_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/7uD6KyQOVCMIR9QdiTaW1V/4ff9790136b683ae320746365ea25381/ca6e5d1fd38951271f515ede1b7caf99201f0526-1460x820.png
content_img_alt: falco-camera
excerpt: >-
  Falco is a behavioral activity monitor designed to detect anomalous activity
  in your applications. Using powerful system call capture technology originally
  built by Sysdig. Falco lets you continuously monitor and detect container,
  application, host, and network activity, all in one place, from one source of
  data, with one set of rules.
seo:
  title: Cloud-native style intrusion and abnormality detection with Falco
  description: >-
    The CNCF has been hard at work over the past few years pushing cloud-native
    technology to new heights. Many of the graduated projects have become
    household names for engineers working at all levels. Projects such as
    Prometheus and Fluentd are being used in production as we speak to solve the
    most pressing issues at scale.
  extra:
    - name: 'og:type'
      value: article
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:title'
      value: Hiking The Grand Canyon
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:description'
      value: >-
        The Grand Canyon is a steep-sided canyon carved by the Colorado River in
        Arizona
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:image'
      value: images/8.jpg
      keyName: property
      relativeUrl: true
      type: stackbit_page_meta_extra
    - name: 'twitter:card'
      value: summary_large_image
      type: stackbit_page_meta_extra
    - name: 'twitter:title'
      value: Hiking The Grand Canyon
      type: stackbit_page_meta_extra
    - name: 'twitter:description'
      value: >-
        The Grand Canyon is a steep-sided canyon carved by the Colorado River in
        Arizona
      type: stackbit_page_meta_extra
    - name: 'twitter:image'
      value: images/8.jpg
      relativeUrl: true
      type: stackbit_page_meta_extra
  type: stackbit_page_meta
template: post
stackbit_url_path: /posts/cloud-native-intrusion-detection
---
## Introduction

The CNCF has been hard at work over the past few years pushing cloud-native technology to new heights. Many of the graduated projects have become household names for engineers working at all levels. Projects such as [**Prometheus**](https://appfleet.com/blog/tag/prometheus/) and [**Fluentd**](https://appfleet.com/blog/part-2-efk-stack-on-kubernetes/) are being used in production as we speak to solve the most pressing issues at scale.

While we all may know about the graduated projects, I'm here to tell you about a less commonly known project that is currently in the Sandbox stage known as **Falco**.

### About Falco

Falco is a behavioral activity monitor designed to detect anomalous activity in your applications. Using powerful [system call capture](https://sysdig.com/blog/fascinating-world-linux-system-calls/) technology originally built by [Sysdig](https://appfleet.com/blog/sysdig-what-it-is-and-how-to-use-it/). Falco lets you continuously monitor and detect container, application, host, and network activity, all in one place, from one source of data, with one set of [rules](https://falco.org/docs/rules).

### What kind of behavior can Falco detect?

Falco can detect and alert on any behavior that involves making [Linux system calls](http://man7.org/linux/man-pages/man2/syscalls.2.html). Falco alerts can be triggered by the use of specific system calls, their arguments, and by properties of the calling process. For example, you can easily detect when:

*   A shell is run inside a container

*   A server process spawns a child process of an unexpected type

*   A sensitive file, like /etc/shadow, is unexpectedly read

*   A non-device file is written to /dev

*   A standard system binary (like ls) makes an outbound network connection

Think of it as a proximity alarm for your infrastructure!

### How to use Falco

Falco is deployed as a long-running daemon which you can install as a [Debian](https://falco.org/docs/installation#debian)/[rpm](https://falco.org/docs/installation#rhel) package on a regular host or container host, you can deploy it as a [container](https://falco.org/docs/installation#docker), or you can build it [from source](https://falco.org/docs/source).

Falco is configured via (1) a [rules file](https://falco.org/docs/rules) that defines which behaviors and events to watch for and (2) a [general configuration file](https://falco.org/docs/configuration). Rules are expressed in a high-level, human-readable language known as YAML

### Falco alerts

When Falco detects suspicious behavior, it sends [alerts](https://falco.org/docs/alerts) via one or more channels:

*   Writing to standard error

*   Writing to a file

*   Writing to Syslog

*   Pipe to a spawned program. A common use of this output type would be to send an email for every Falco notification.

### Installing Falco

Install Falco directly on Linux via a scripted install, package managers, or configuration management tools like Ansible. Installing Falco directly on the host provides:

*   The ability to monitor a Linux host for abnormalities. While many use cases for Falco focus on running containerized workloads, Falco can monitor any Linux host for abnormal activity, containers (and Kubernetes) being optional.

*   Separation from the container scheduler (Kubernetes) and container runtime. Falco running on the host removes the container scheduler from the management of the Falco configuration and Falco daemon. This can be useful to prevent Falco from being tampered with if your container scheduler gets compromised by a malicious actor.

#### Scripted install

1.  To install Falco on Linux, you can download a shell script that takes care of the necessary steps:

<!---->

1.  Then verify the SHA256 checksum of the script using the sha256sum tool (or something analogous):

It should be: ecd5517492ebb356b820f404aea4afcb9d2d81bf98c55a8174b050c5bbc7092a.

1.  Then run the script either as root or with sudo:

#### Package install

*   **RHEL**

Trust the Draios GPG key and configure the yum repository:

Install the EPEL repository:

**Note** — The following command is required only if DKMS is not available in the distribution. You can verify if DKMS is available using yum list dkms. If necessary, install it using:

Install kernel headers:

**Warning** — The following command might not work with any kernel. Make sure to customize the name of the package properly.

Install Falco:

To uninstall, run yum erase falco.

*   **Debian**

Trust the Draios GPG key, configure the apt repository, and update the package list:

Install kernel headers:

**Warning** — The following command might not work with any kernel. Make sure to customize the name of the package properly.

Install Falco:

To uninstall, run apt-get remove falco

#### Config Management Systems

You can also install Falco using configuration management systems like [Puppet](https://falco.org/docs/installation/#puppet) and [Ansible](https://falco.org/docs/installation/#ansible).

##### **Puppet**

A [Puppet](https://puppet.com/) module for Falco, sysdig-falco, is available on [Puppet Forge](https://forge.puppet.com/sysdig/falco/readme).

##### **Ansible**

[@juju4](https://github.com/juju4/) has helpfully written an [Ansible](https://ansible.com/) role for Falco, juju4.falco. It’s available on [GitHub](https://github.com/juju4/ansible-falco/) and [Ansible Galaxy](https://galaxy.ansible.com/juju4/falco/). The latest version of Ansible Galaxy (v0.7) doesn’t work with Falco 0.9, but the version on GitHub does.

#### Running Falco as a service

Once you’ve [installed](https://falco.org/docs/installation) Falco as a package, you can start the service:

The default configuration logs events to syslog.

The current version of Falco is available as the falcosecurity/falco:0.18.0 container. Here’s an example command to run the container locally on Linux:

By default, starting the container will attempt to load and/or build the Falco kernel module. If you already know that the kernel module is loaded and want to skip this step, you can set the environment variable SYSDIG_SKIP_LOAD to 1:

### Example Behavior

Here is an example of a type of behavior that Falco can detect. In this case, notifying when a shell is run in a container, which you could then follow up on by taking action through serverless (FaaS) frameworks, or other automation.

### Taking Action: Setting up alerts with Slack

Now that you've gotten Falco up and running, why don't we try and generate some dummy events and simulate a potential action we would have Falco take when triggered. In this scenario, we are going to use a handy daemon that the team at Sysdig created called [falcosidekick](https://github.com/falcosecurity/falcosidekick). What it does is it essentially takes Falco event data and forwards it to different outputs.

### Add config

You'll need to add a bit of config (adapted to your environment) to your falco.yaml :

### Running falcosidekick

To get started, let's go ahead and pull the image from Docker Hub:

You'll have to explicitly append a tag to the command since for whatever reason there is no latest tag in the repo.

Next, you'll obviously want to run the image, specifying either env variables or a config file in a .yaml format. The command using env variable should look something like this:

If you decide to go with a config file, you can find a sample file [here](https://github.com/falcosecurity/falcosidekick/blob/master/config_example.yaml).

### Pushing dummy event data to Slack

Before we push any data, you'll want to create a sandbox workspace and app with incoming webhooks enables as described [here](https://api.slack.com/messaging/webhooks).

Once you have your webhook from that setup, go ahead and run the Falco [Event Generator](https://hub.docker.com/r/sysdig/falco-event-generator/) image to begin generating dummy events to alert Slack:

It is *strongly* recommended that you run the event generator in a container as it modifies files and directories below /bin, /etc, /dev, etc.

Once you've gotten that spun up, you should see the event being generated to Slack, like in this screenshot:

## All Done!

Well, there you have it! You've now gotten a crash course in Sysdig's real-time threat monitoring framework and alerting solution. I found this to be a really cool tool for the DevSecOps landscape and I'm even thinking about trying to spin up an Alexa skill for alerts with this. My mind is buzzing thinking about the applications. It really is like a proximity alarm for your infrastructure. It's now going to be 6am where I am, so I'm tired now, and I'm going to go to bed. Have a great New Year everyone!
